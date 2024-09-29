---
layout:      project  # Must be set to project
date:        1 Aug 2024
title:       Cloud-based Video Compressor
caption:     Using FFMPEG
image:
  path:      /assets/thumb/ffmpeg.webp
  srcset:
    1280w:   /assets/thumb/ffmpeg.webp
    960w:    /assets/thumb/ffmpeg@960w.webp
    480w:    /assets/thumb/ffmpeg@480w.webp
    240w:    /assets/thumb/ffmpeg@240w.webp
# description: >
#   Screenshot of the web app.
links:
  - title:   Web App
    url:     https://ffmpeg-app-qnqwehm7zq-uw.a.run.app
featured:    false
sitemap:     true
---

**[Try out the app here!](https://ffmpeg-app-qnqwehm7zq-uw.a.run.app)**

## Background & Goals

My friends and I frequently use Discord to communicate and keep in touch, but we often run into the 10 MB filesize limit when sharing videos. This significantly impedes our ability to share cat videos and game clips.

So, I wanted to make a simple web app that could be shared with my friends which does video compression. The online goal is so that we could compress videos without needing to install any heavy-weight video editing program on our local computers. Not everyone has access to Adobe Premiere or DaVinci Resolve, or wants to use FFmpeg on the command line.

The web app should be easy to use, and output videos with good visual quality. Other free online video compressors are decent at lowering filesize, but result in blurry videos full of visual artifacts. Plus, they don't target the 10 MB filesize limit---leaving users disappointed after waiting many minutes just to have a video that's still too big for Discord.

To summarize, my goals for this project were to create an app that:
- Efficiently transcodes and compresses short videos, <5 minutes, so that the resulting video is <10 MB
- Balances video quality with processing speed
- Videos are not saved on the server---fortunately, this means a database is not needed
- Is available online for non-technical users
- Does not make me accidentally spend [$100K](https://old.reddit.com/r/Firebase/comments/17mv8r0/firebase_bill_of_121000_for_last_2_days/) [on](https://blog.tdwright.co.uk/2018/09/06/beware-runonstartup-in-azure-functions-a-serverless-horror-story/) [cloud](https://thenable.io/how-a-recursive-lambda-function-cost-hundreds-of-dollars) [computing](https://old.reddit.com/r/AZURE/comments/1arbror/ever_run_up_a_big_cloud_bill_by_accident_tell_me/) [resources](https://cloudsoft.io/blog/the-curious-case-of-the-spiralling-aws-lambda-bill).


## Current App State

It works! The cloud server takes a few seconds for a cold start, then accepts requests to compress videos. Just drag-and-drop the desired video onto the indicated area. The UI is very barebones, but the emphasis of this project is on functionality and ease-of-use.

Known issues:
- On smartphones, the webpage must be kept open so that it continuously sends polling requests. This is needed to keep the cloud server instance alive to continue processing the video. Switching apps or tabs on mobile devices typically stops the requests.
- There's no error checking to verify that you upload a video. If FFmpeg can't process the uploaded file, then nothing really will happen.
- The server can reasonably process 2 videos at once, but Google Cloud begins to rate-limit the other video compression requests after that, returning an error code. The remaining videos can be re-uploaded once the first couple of videos are done being processed.


## Approach

Inspired by Fireship's [How I deploy serverless containers for free](https://www.youtube.com/watch?v=cw34KMPSt4k), I use **_Google Cloud Run (GCR)_** to host my web app, which is packed into a Docker container that runs Flask and FFmpeg. FFmpeg will use x265 encoding to efficiently compress the videos, which are typically encoded using older codecs that have a worse quality vs. file-size tradeoff.

Like Fireship's app, which removes image backgrounds, my app is configured on GCR to only boot up when processing data and be asleep the rest of the time. Thus, keeping within GCR's free tier of hosting. Keeping instances constantly alive or using other services, such as a full VM, could easily push the app into the paid tier of the cloud services.

But unlike Fireship's app, which processes image data in seconds and returns it, my FFmpeg app will need to process videos over many minutes. Video compression requests could easily take over the 6 minute timeout limit imposed by GCR. How do I tackle this problem (and many other problems) that arose while developing this app? Find out more below!


## Obstacle 1: "It works on my machine!"

When developing and testing the initial version of the app locally, **running FFmpeg automatically utilized all available CPU cores on my computer**. (I opted for CPU-based compression, as it tends to give better results than GPU-based compression. Also, GPU access on GCR servers was still in beta.) Unfortunately, when I first deployed my app to the cloud, it seemed to only utilize 1 vCPU. But, I had configured it to the maximum of 8 vCPUs. Why wasn't the app automatically scaling to use all available compute on the cloud server?

The total CPU usage when processing a video on the cloud was limited to ~10%. I suspected only one core was being used since this was less than $$1/8 = 12.5\%$$. Likewise, when I configured the container to use 4 vCPU cores, the utilization was upper-bounded by $$1/4 = 25\%$$.

![cpu_util_bad.webp](/assets/webp/ffmpeg-app/cpu_util_bad.webp){:.lead width="945" height="249" loading="lazy"}
Container CPU max utilization seems to be limited to 10%, why?
{:.figcaption}

After reading through GCR documentation, searching online, and trying various fixes, I determined that this was likely a NUMA-related issue with the server CPUs. From Wikipedia, 
> Non-uniform memory access (NUMA) is a computer memory design used in multiprocessing, where the memory access time depends on the memory location relative to the processor. Under NUMA, a processor can access its own local memory faster than non-local memory (memory local to another processor or memory shared between processors).

Looking at the FFmpeg logs output on the server, there were some hints that confirmed my suspicions:

```
x265 [warning]: No thread pool allocated, --wpp disabled
x265 [warning]: No thread pool allocated, --lookahead-slices disabled
```

To fix this, when the app calls `ffmpeg` in a subprocess, I additionally pass in `-x265-params numa-pools=8`. This essentially tells the x265 encoder to be aware that we're working in a NUMA-enabled system, and that there are 8 vCPUs to utilize. It optimizes how the encoding process distributes its workload across the different CPU cores and their associated memory. With this change, I was able to _sporadically_ get high utilization. But, average CPU usage was still low.

## Obstacle 2: "It works on my machine! But still not on the cloud."

I continued investigating why average CPU usage was low when compressing the videos. It took more digging, but it turns out the issue was due to how I configured the cloud instance. In an effort to save on compute (and therefore money), I set it so that "CPU is only allocated during request processing."

![configure-allocation.webp](/assets/webp/ffmpeg-app/configure-allocation.webp){:.lead width="767" height="128" loading="lazy"}

Using the "CPU is always allocated" option exceeds the monthly free vCPU-second allowed by GCR. Plus, it would be a waste of electricity to keep the app running when nobody was using it.

However, the issue with "CPU is only allocated during request processing" is that CPU resources are de-allocated when there is no request being processed. My app is setup so that FFmpeg does processing in the background in its own thread. The user uploads a video via a request, video compression starts in another thread, and the server responds OK to the request. But due to the Google Cloud Run optimization, as soon as the OK response is sent then CPU is de-allocated, meaning there is no compute available for the video compression thread in the background.

![gcr-allocation-mode.webp](/assets/webp/ffmpeg-app/gcr-allocation-mode.webp){:.lead width="767" height="128" loading="lazy"}
From the [GCR documentation](https://cloud.google.com/run/docs/configuring/cpu-allocation).
{:.figcaption}

In reality, GCR doesn't instantly de-allocate the CPU resources and shutdown the web app instance. Background tasks are still allowed to run, just with heavily restricted CPU usage. Hence, average CPU utilization is still low despite spikes of high utilization during request processing. At this point, the app still worked and was able to compress videos, but it took roughly **25x the time length of the video** to compress on the GCR server. After 15 minutes without any request though, GCR does shutdown the app instance---new visitors to the page will have to wait a few seconds for the app to start up again.

One way to solve this de-allocation issue is to bring the video compression function to the foreground instead. In this scenario, a user makes a request, the video compresses, and the server responds with the compressed video. However, there are several problems with this approach.
First, we don't respond with an OK to tell the user that the upload was successful. They are left wondering about the progress of the compression as the server silently carries on.
Second, without an OK response telling the client-side app where to poll compression progress, we can't make polling requests to check video compression status without requiring serial processing. This setup would limit our ability to process multiple videos at once in parallel.
Third, GCR limits response delays to 6 minutes, so we would be limited to 6 minutes of processing time which is problematic for longer videos.

A better way to solve the de-allocation issue, which is what I implemented, is to use the progress polling requests to keep the vCPUs "alive" and allocated. Like before, a user uploads a video via a request, video compression starts in another thread, and the server responds OK to the request and tells the client-side app a URL/route to poll for progress information. When requesting the video compression status, the server simply returns a percentage from 0-100% of the compression progress. The polling request happens every 2 seconds.

The trick, however, is that the server-side app delays the response by 3 seconds. Thus for every 2 seconds of processing, the app retains full vCPU allocation for 3 seconds. This means the video compression thread in the background is able to continuously utilize all 8 available vCPUs, and we approach 100% utilization while processing a video. Once a video is done being processed, it is automatically downloaded to the client-side and polling stops. The instance goes back to sleep, and then is automatically shut down if no new request come in.

![cpu_util_good.webp](/assets/webp/ffmpeg-app/cpu_util_good.webp){:.lead width="933" height="245" loading="lazy"}
Achieving near-100% CPU utilization during video compression.
{:.figcaption}

Now that the computing resources were being properly utilized, video compression time was down to about **4x the time length of the video**. E.g. a 1 minute video takes about 4 minutes to compress.

In my casual testing, compression would only take about 2-3x the video length amount of time using the "medium" FFmpeg preset, if we did not have a target filesize. However, since we want the output video to be less than 10 MB, we need to use the two-pass method for encoding. This raises the processing time to 4x, but is necessary to achieving the 10 MB goal.


## Obstacle 3: 10 MB Target Filesize

[Two-pass Encoding](https://trac.ffmpeg.org/wiki/Encode/H.265#Two-PassEncoding) is needed to target a specific output filesize, in our case 10 MB. When encoding, we need to specify the video bitrate in terms of kilobits per second. So we calculate:

```
filesize: 10 MB = 80,000 kb
Total bitrate: 80,000 kb / (video duration in seconds) = N kb/s
Target video bitrate: (N - (audio bitrate)) kb/s
```

The video duration in seconds and audio bitrate is parsed from the FFmpeg console output, in the metadata section. Because running the two-pass method requires knowing the target video bitrate ahead of time, we actually run the `ffmpeg` command with dummy parameters to get that metadata ahead of time. So `ffmpeg` is run three times for each video: once for metadata, and twice for the two-pass method. The bitrate calculation is done for each video.

Example command that is run just to quickly get the metadata, which is parsed in Python:
```bash
ffmpeg -i input.mp4 -map 0:v:0 -c copy -f null - 
```
Then example first-pass of the encoding:
```bash
ffmpeg -y -i input.mp4 -vcodec libx265 -b:v 2000k -x265-params pass=1:numa-pools=8 -preset medium -an -pix_fmt yuv420p -f mp4 /dev/null
```
And example second-pass of the encoding
```bash
ffmpeg -i input.mp4 -vcodec libx265 -b:v 2000k -x265-params pass=2:numa-pools=8 -preset medium -c:a copy -pix_fmt yuv420p -f mp4 output.mp4
```

Readers experienced with `ffmpeg` might notice the `-f mp4` to force the `.mp4` format. A sub-obstacle that I ran into was that `.mp4` videos that had been previously concatenated together, would be corrupted after compression. Using the `-f mp4` argument fixed that video corruption issue.


## Obstacle 4: 32MiB Request Limit

Google Cloud Run limits the size of requests to 32 mebibytes. This limits the filesize of the videos that we can upload to the server for processing. This is especially problematic for our use case, which is to upload large files to a server for compression.

Fortunately, I was able to use [Dropzone.js](https://www.dropzone.dev/), an open source library for drag-and-drop file uploads. It features a chunked upload option for splitting up the video into <32 MiB chunks to send to the server. After sending the chunks to the server, the full video is reassembled for the video compression step.

Files are first temporarily saved within the container with a `secure_filename` during the upload process. Then once all chunks are loaded, the video is renamed to a randomized name to avoid potential filename conflicts. The server then uses `ffmpeg` to compress the video, as described above, and sends the result back to the client. Because the videos are saved into the container, and not a volume or a database, they disappear as soon as the container instance is shutdown---15 minutes after no more requests are received. This both enhances user privacy, and makes my job easier not having to mount a volume or connect to a database.


## Conclusion

I definitely ran into many obstacles when developing this app, many of them on the relatively minor side that aren't worth writing up a whole section on. But overall, this was a great experience adventuring into different domains and learning new things.

Topics that I was previously unfamiliar with, but now have a much better understanding and appreciation of:
- Video compression techniques, codecs, etc.
- Configuring cloud resources and setting up a webapp from scratch
- Writing plain HTML + Javascript---there's a reason people like to use frameworks like React or Flutter!
- Flask, creating a webapp from scratch with Python

I'll likely keep working on this app in my spare time! And will update this as I go along. Some future ideas to implement are:
- Improved GUI---some color would liven up the site!
- HTML/Javascript dropdown boxes for selecting compression options: presets, codecs, target filesize, etc.
- Better error messages from the server, and displaying them on the client-side.

