---
layout:      project  # Must be set to project
date:        3 Jun 2017
title:       Frequencies & Gradients
caption:     _Lincoln in Dalivision_ <br> Do you see Lincoln or a woman?
image:
  path:      /assets/thumb/cs194-26-3.webp
  srcset:
    776w:    /assets/thumb/cs194-26-3.webp
    480w:    /assets/thumb/cs194-26-3@480w.webp
    240w:    /assets/thumb/cs194-26-3@240w.webp
description: >
  *Lincoln in Dalivision*. Depending on how close or far you look at this image, you may either see an image of Abraham Lincoln or a woman looking out the window into a sunset. (Squinting your eyes may also work.)
# links:
#   - title:   Demo
#     url:     /assets/pdf/pdf.pdf
featured:    false
sitemap:     true
---

# Frequencies & Gradients

<div class="row">
<div class="col col-sm-6">
<a href="2_4zero/2_4_blendedpas0.webp">
<img src="/assets/webp/cs194-26/3/2_4zero/2_4_blendedpas0.webp" class="img-responsive center-block" width="450px"></a>
</div>
<div class="col col-sm-6">
<a href="1_4_c/1_4_im_lc.webp">
<img src="/assets/webp/cs194-26/3/1_4_c/1_4_im_lc.webp" class="img-responsive center-block" width="300px"></a>
</div>
</div>

<h2>Part 1 - Frequency Domain Processing</h2>

<h3>1.1 - Sharpening Images</h3>
<p>
Edges in an image are represented by high frequency changes among pixels. So to sharpen images, we can increase the magnitudes of the higher frequencies in the frequency domain. One way to approach that goal is through the "unsharp masking" technique.
<br><br>
For the unsharp masking technique, first we obtain the lower frequency signals in an image by applying a Gaussian blur to the entire image. A Gaussian blur effectively removes higher frequencies from an image. Then we subtract the blurred image from the original to obtain the Laplacian of the image. The Laplacian contains the difference in signals of the original and the blurred image, which are the high frequency signals that were removed from the blurring process. Finally, the Laplacian is positively scaled and added back onto the original image. This results in an image where the higher frequencies are boosted in magnitude.
</p>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-6">Before</th>
<th class="col-sm-6">After</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="1_1/1_1_in1.webp">Flower, Original
<img src="/assets/webp/cs194-26/3/1_1/1_1_in1.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_1/1_1_im1.webp">Flower, Sharpened
<img src="/assets/webp/cs194-26/3/1_1/1_1_im1.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="1_1/1_1_in2.webp"><em>Water Lilies</em>, Monet, Original
<img src="/assets/webp/cs194-26/3/1_1/1_1_in2.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_1/1_1_im2.webp"><em>Water Lilies</em>, Monet, Sharpened
<img src="/assets/webp/cs194-26/3/1_1/1_1_im2.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>

<h3>1.2.1 - Hybrid Images</h3>
<p>
Images contain a wide range of frequencies. From a long distance, humans tend to perceive low frequency signals in an image better. And from a short distance, humans tend to perceive high frequency signals in an image better.
<br><br>
By combining the low frequency components of one image and the high frequency components of another image, we can create a hybrid image. The hybrid image will look differently at different distances. There are many ways to implement these hybrid images, such as using different filtering techniques.
<br><br>
Here, I use the Gaussian blurring technique to process the images. The low frequencies are obtained directly by Gaussian blurring, as in part 1.1. The high frequencies are obtained by subtracting the Gaussian blurred image from the original, also as in part 1.1. The two resulting images are averaged to create the hybrid.
<br><br>
Try looking at the following images close to the screen (seeing high frequencies), and then far away from the screen (seeing low frequencies). Using different zoom levels on the webpage work as well.
</p>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-4">Low Frequencies From:</th>
<th class="col-sm-4">High Frequencies From:</th>
<th class="col-sm-4">Hybrid Image</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="nutmeg.webp">Nutmeg
<img src="/assets/webp/cs194-26/3/nutmeg.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="DerekPicture.webp">Derek
<img src="/assets/webp/cs194-26/3/DerekPicture.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_2_Dn/1_2_out_Dn.webp">Nutrek
<img src="/assets/webp/cs194-26/3/1_2_Dn/1_2_out_Dn.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="cowflipped.webp">Cow
<img src="/assets/webp/cs194-26/3/cowflipped.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="elephant.webp">Elephant
<img src="/assets/webp/cs194-26/3/elephant.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_2_ce/1_2_out_ce.webp">Cophant
<img src="/assets/webp/cs194-26/3/1_2_ce/1_2_out_ce.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="lion.webp">Lion
<img src="/assets/webp/cs194-26/3/lion.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="corgi.webp">Corgi
<img src="/assets/webp/cs194-26/3/corgi.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_2_lc/1_2_out_lc.webp">Liorgi
<img src="/assets/webp/cs194-26/3/1_2_lc/1_2_out_lc.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>

<h3>1.2.2 - Analysis of Hybrid Images</h3>
<p>
For the lion-corgi hybrid image, the following is a frequency-domain analysis of the hybridization process. The plots are log absolute magnitudes of 0-centered 2D FFT images.
<br><br>
In MATLAB code: <code>imagesc(log(abs(fftshift(fft2(gray_image)))))</code>
<br><br>
The 2nd transform, containing mostly low frequencies, is characterized by the bright vertical and horizontal lines intersecting at the center. The 4th transform, containing mostly high frequencies, is charaterized by the bright sqiggles away from the center. Its center is a low-value teal color. The 5th transform, being the hybrid image, has both the bright vertical and horizontal lines, and the squiggles.
</p>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-5ths"></th>
<th class="col-sm-5ths"></th>
<th class="col-sm-5ths"></th>
<th class="col-sm-5ths"></th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="lion.webp">Lion
<img src="/assets/webp/cs194-26/3/lion.webp" class="img-responsive center-block" width=""></a>
</td>
<td>
</td>
<td><a href="corgi.webp">Corgi
<img src="/assets/webp/cs194-26/3/corgi.webp" class="img-responsive center-block" width=""></a>
</td>
<td>
</td>
<td><a href="1_2_lc/1_2_out_lc.webp">Liorgi
<img src="/assets/webp/cs194-26/3/1_2_lc/1_2_out_lc.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="1_2_lc/1_2_2_im1f0.webp">Transform of Original
<img src="/assets/webp/cs194-26/3/1_2_lc/1_2_2_im1f0.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_2_lc/1_2_2_im1f1.webp">Transform of Low Frequency Image
<img src="/assets/webp/cs194-26/3/1_2_lc/1_2_2_im1f1.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_2_lc/1_2_2_im2f0.webp">Transform of Original
<img src="/assets/webp/cs194-26/3/1_2_lc/1_2_2_im2f0.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_2_lc/1_2_2_im2f1.webp">Transform of High Frequency Image
<img src="/assets/webp/cs194-26/3/1_2_lc/1_2_2_im2f1.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_2_lc/1_2_2_im12f0.webp">Transform of Hybrid Image
<img src="/assets/webp/cs194-26/3/1_2_lc/1_2_2_im12f0.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>
<br>
<h3>Failure Case</h3>
<p>
As mentioned above, there are multiple ways to implement the combining of low and high frequencies. Instead of applying Gaussian blur in the image domain to extract frequencies, I also attempted to apply box filters in the frequency domain.
<br>
Convolving with a Gaussian function in the image domain is equivalent to multiplying by a different Gaussian function in the frequency domain. But, multiplying by a box function in the frequency domain is equivalent to convolving with a sinc function in the image domain. Due to the fact that a box filter has discrete edges/is not continuous, the change in frequencies are no longer smooth like in the Gaussian case. This means the resulting hybrid image has jarring visual artifacts, and is not as pleasant to look at. There is no natural "transition" going from perceiving one image to the other one, they both appear overlapped.
</p>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-5ths"></th>
<th class="col-sm-5ths"></th>
<th class="col-sm-5ths"></th>
<th class="col-sm-5ths"></th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="nutmeg.webp">Nutmeg
<img src="/assets/webp/cs194-26/3/nutmeg.webp" class="img-responsive center-block" width=""></a>
</td>
<td>
</td>
<td><a href="DerekPicture.webp">Derek
<img src="/assets/webp/cs194-26/3/DerekPicture.webp" class="img-responsive center-block" width=""></a>
</td>
<td>
</td>
<td><a href="1_2_fail/1_2_im12x1.webp">Nutrek
<img src="/assets/webp/cs194-26/3/1_2_fail/1_2_im12x1.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="1_2_fail/1_2_1_im1f0.webp">Transform of Original
<img src="/assets/webp/cs194-26/3/1_2_fail/1_2_1_im1f0.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_2_fail/1_2_1_im1f1.webp">Transform of Low Frequency Image
<img src="/assets/webp/cs194-26/3/1_2_fail/1_2_1_im1f1.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_2_fail/1_2_1_im2f0.webp">Transform of Original
<img src="/assets/webp/cs194-26/3/1_2_fail/1_2_1_im2f0.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_2_fail/1_2_1_im2f1.webp">Transform of High Frequency Image
<img src="/assets/webp/cs194-26/3/1_2_fail/1_2_1_im2f1.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_2_fail/1_2_1_im12f0.webp">Transform of Hybrid Image
<img src="/assets/webp/cs194-26/3/1_2_fail/1_2_1_im12f0.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>

<h3>1.3 - Gaussian and Laplacian Stacks</h3>
<p>
In a Gaussian stack, each image is the previous image with a Gaussian blur applied.
<br><br>
In a Laplacian stack, each image is the difference between two images. In my stack, an image in the Laplacian stack is the difference between the Gaussian stack image directly above it, and the Gaussian stack image up and to the right of it. The last Laplacian image in my stack is just the final Gaussian image.
<br><br>
Essentially, the Laplacian stack captures the loss in frequencies as the Gaussian blur is applied at each level of the Gaussian stack.
<br><br>
The following images have the Gaussian stacks lined up on the top row, and the Laplacian stacks lined up on the bottom row. Please click-to-enlarge or zoom in on the following images.
<br><br>
For the Liorgi stacks, notice that the leftmost Laplacians looks like a corgi, and then the rightmost Laplacians look like a lion. The Gaussian levels simulate viewing the hybrid image at different distances. For the Gala stacks, the left side looks like Gala contemplating the mediterranean sea out of a window. The right side looks like Abraham Lincoln. Again, the Gaussian levels simulate viewing the hybrid image at different distances.
</p>

<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-12">Gaussian stack images on top, Laplacian stack images on bottom</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="1_3/1_3_im12xx.webp">Liorgi Stacks
<img src="/assets/webp/cs194-26/3/1_3/1_3_im12xx.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="1_3/1_3_imgalaxx.webp"><em>Gala Contemplating the Mediterranean Sea which at Twenty Meters Becomes the Portrait of Abraham Lincoln</em> a.k.a. <em>Lincoln in Dalivision</em>, Dali
<img src="/assets/webp/cs194-26/3/1_3/1_3_imgalaxx.webp" class="img-responsive center-block" width=""></a>
<table class="table table-striped">
</td>
</tr>
</tbody>
</table>

<h3>1.4.1 - Multiresolution Blending</h3>
<p>
We can blend different images together using Gaussian and Laplacian stacks. Since Laplacian stacks represent different buckets of frequencies, we can blend those buckets individually. Then adding the blended bucket-images together will result in an overall blended image. Using a mask, this technique works for irregular shapes in images.
<br><br>
Normally, when we cut-and-paste one image onto another image, there will be a visible seam. It would be clear that the two image don't match up and we can tell exactly where the two images don't match. But if we blend high frequencies in a small window around a mask and low frequencies in a large window around a mask, the image becomes better blended. There would no longer be a seam, caused by a high frequency change in the image signal. Instead there would be a smooth transition from one image to the next.
<br><br>
More in depth, to do multiresolution blending we need: Laplacian stack of image $A$, Laplacian stack of image $B$, and a Gaussian stack of a mask for combining image $A$ into image $B$. Notice that higher levels of the Laplacian stack correspond to lower frequency buckets. And also notice that as the Gaussian level increases, that level's mask represents a larger "blending window." (See images for clarification.)
<br><br>
At each level, the frequency bucket is blended by the formula $$bucket(i) = L_A(i)\times G_M(i) + L_B(i)\times(1-G_M(i))$$ Where $L_B(i)$ is the Laplacian of image $A$ at level $i$, and $G_M(i)$ is the Gaussian of the mask for $A$ at level $i$. The final image is obtained by adding all the frequency buckets together into one image.
</p>

<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-12">Algorithm Example, Apple, Orange, and Result</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="1_4/1_3_im_T_orangexx.webp">Apple Stacks
<img src="/assets/webp/cs194-26/3/1_4/1_3_im_T_orangexx.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="1_4/1_3_im_T_maskxx.webp">Mask Stacks - Notice how the window around the seam grows wider as level increases
<img src="/assets/webp/cs194-26/3/1_4/1_3_im_T_maskxx.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="1_4/1_3_im_T_applexx.webp">Orange Stacks
<img src="/assets/webp/cs194-26/3/1_4/1_3_im_T_applexx.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="1_4/1_4_im_appleorange.webp">Result of Multiresolution Blending
<img src="/assets/webp/cs194-26/3/1_4/1_4_im_appleorange.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>

<h3>1.4.1 - Multiresolution Blending Results, in Color!</h3>
<p>
In order to blend colored images, the technique described above was applied to each RGB color channel separately.
</p>
<p>
This technique works best when the backgrounds are similar; or when one image provides the entirety of a background, and the other is overlayed on top.
<br><br>
So all three work well except for the Colephant (Cow-elephant), as the background for those two images differ.
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-6"></th>
<th class="col-sm-6"></th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="1_4_c/1_4_im_ao.webp">Apprange
<img src="/assets/webp/cs194-26/3/1_4_c/1_4_im_ao.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_4_c/1_4_im_ce.webp">Colephant
<img src="/assets/webp/cs194-26/3/1_4_c/1_4_im_ce.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="1_4_c/1_4_im_la.webp">Lipple
<img src="/assets/webp/cs194-26/3/1_4_c/1_4_im_la.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_4_c/1_4_im_lc.webp">Liorgi
<img src="/assets/webp/cs194-26/3/1_4_c/1_4_im_lc.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-3">Background Image</th>
<th class="col-sm-3">Foreground Image</th>
<th class="col-sm-3">Mask</th>
<th class="col-sm-3">Output</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="corgibody.webp">
<img src="/assets/webp/cs194-26/3/corgibody.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="lionface.webp">
<img src="/assets/webp/cs194-26/3/lionface.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="lionmask.webp">
<img src="/assets/webp/cs194-26/3/lionmask.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_4_c/1_4_im_lc.webp">
<img src="/assets/webp/cs194-26/3/1_4_c/1_4_im_lc.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>


<br>
<br>
<br>
<br>
<br>
<h2>Part 2 - Gradient Domain Processing</h2>
<p>
The gradients of an image describe how pixel intensities change from pixel to pixel. As images are two dimensional, gradients exist in both vertical and horizontal directions. By working in the gradient domain, we can blend pictures together while retaining their important features and individual textures.
</p>

<h3>Part 2.1 - Toy Problem</h3>
<p>
Gradients describe how an image change from pixel to pixel. Given an image, we can extract all of the gradient information from it. Then given a single seed pixel, say a corner pixel, we can fully reconstruct an image. If the seed pixel is the same as the pixel in the original image, the reconstruction is extremely small error compared to the original. That is, they are essentially the same image. If the seed pixel is different, then the resulting picture ends up being colored differently. But the textures and figures within the image remain visible. In a way, it can be said that human perception focuses gradients, changes in an image, rather than just seeing colors.
<br><br>
The following image had its gradient information extracted, and then the second image was reconstructed from the top left corner pixel. The RMS error between the two images is <code>0+2.0954e-06i</code>.
</p>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-6">Original Image</th>
<th class="col-sm-6">Reconstructed Image</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="toy_problem.webp">
<img src="/assets/webp/cs194-26/3/toy_problem.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_1/2_1_out.webp">
<img src="/assets/webp/cs194-26/3/2_1/2_1_out.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>

<h3>Part 2.2.1 - Poisson Blending</h3>
<p>
Using the concept of gradients, we can blend together two images while keeping their textures apparent. The color of the input images could end up changing, but the end result is that there are few noticeable artifacts in the output.
<br><br>
On a high level, we again need: source image $S$, background image $B$, and mask. Let $M$ be the masked region from applying the mask to $S$. We will mask the source image to get the object we want, and place it into the background image. Let $R$ be the resulting image. $R$ is equivalent to $B$ everywhere except the masked location. We keep the background the same. Then for pixels inside the mask, we use the gradients of $S$ at that location. Thus, retaining the textures of image $S$. For the border of $M$, where $S$ and $B$ meet to form a seam, we pick the pixel values of $B$ and the gradients of $B$. This ensures the colors in the masked region match the tone of $B$, and ensures there is a smooth transition of textures from $B$ to $S$. The colors from $B$ propogate into the masked region based on gradient values.
<br><br>
Mathematically, the desired pixel values $\textbf{v}$ inside the masked region can be described by
$$\textbf{v} = \text{argmin}_{\textbf{ v}} \sum_{i\in M, j\in N_i \cap M} ((v_i - v_j) - (s_i - s_j))^2 + \sum_{i\in M, j\in N_i \cap -M} ((v_i - b_j) - (s_i - s_j))^2$$
Where we're minimizing the error in choice of $\textbf{b}$. $v_i \in \textbf{v}, s \in S, b \in B$. $M$ is the masked region. $N_i$ refers to the 4-neighborhood of pixels (up, down, left, right) of pixel $i$. $N_i\cap M$ are neighbor pixels in the masked region. $N_i\cap M$ are neighbor pixels NOT in the masked region.
<br><br>
The first summation's expression $(v_i - v_j) - (s_i - s_j)$ seeks to minimize the differences between the output gradients and the source image's gradients. The second summation's expression $(v_i - b_j) - (s_i - s_j)$ seeks to minimize the differnces in output gradients and the background image's gradients at the border of the mask.
<br><br>
This formula is implemented as a linear system of equations in the code. $A\textbf{v}=b$, where we construct $A$ and $b$. First given a mask, we find the 1 pixel border of the mask. Then we do the following on all three color channels:
<br><br>
For each pixel on the border, we add an equation to the system assigning variable $v_i$ to be that pixel's intensity. A $1$ is assigned to $A$ for equation number $e$, and the pixel intensity is assigned to index $e$ in vector $b$. Then for all pixels in the mask, we find the gradients in all four directions, using the corresponding gradient from $S$. A $4$ and four $-1$s are assigned to appropriate locations in $A$ on line $e$, and the sum of the four directional gradients are assigned to the corresponding index $e$ in $b$. Once the assignment is finished, a sparse matrix least squares solver operates on $A\textbf{v}=b$ and returns the optimal $\textbf{v}$. $\textbf{v}$ is the size of the mask $M$, as it describes the pixel values in $M$. $\textbf{v}$ is reshaped and assigned to the masked region to produce the output channel image. Again, this procedure is run on all three color channels separately. The resulting channels are stacked to produce the final output.
<br><br>
Here are the results of Poisson blending:
</p>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-5ths">Source</th>
<th class="col-sm-5ths">Mask</th>
<th class="col-sm-5ths">Background</th>
<th class="col-sm-5ths">Direct Copy</th>
<th class="col-sm-5ths">Poisson Blending</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="2_2n3_al/2_2_im_s.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_al/2_2_im_s.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_al/2_2_mask_s.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_al/2_2_mask_s.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="apple.jpeg">
<img src="/assets/webp/cs194-26/3/apple.jpeg" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_al/2_2_direct.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_al/2_2_direct.webp" class="img-responsive center-block" width=""></a>
</td>                <td><a href="2_2n3_al/2_2_blendedal.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_al/2_2_blendedal.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>

<h3>Part 2.2.2 - Poisson Blending Results</h3>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-4">Source</th>
<th class="col-sm-4">Background</th>
<th class="col-sm-4">Poisson Blending</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="2_2n3_al/2_2_im_s.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_al/2_2_im_s.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="apple.jpeg">
<img src="/assets/webp/cs194-26/3/apple.jpeg" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_al/2_2_blendedal.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_al/2_2_blendedal.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="2_2n3_pd/2_2_im_s.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_pd/2_2_im_s.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_pd/2_2_background.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_pd/2_2_background.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_pd/2_2_blendedpd.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_pd/2_2_blendedpd.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="2_2n3_peng/2_2_im_s.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_peng/2_2_im_s.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_peng/2_2_background.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_peng/2_2_background.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_peng/2_2_blendedip.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_peng/2_2_blendedip.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>

<h3>Part 2.2.3 - Poisson Blending Failure</h3>
<p>
The blending only works well if the background image is mostly the same texture and color. Here, placing the source penguin onto the tree-snow line causes a failure in the algorithm. The failure is likely due to the fact that the border contains two very distinct colors: white and green. Thus the least squares solution has a large error as it tries to resolve matching the masked region to both white and green colors. As shown, the penguin gets confused and becomes an evil penguin.
</p>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-4">Source</th>
<th class="col-sm-4">Background</th>
<th class="col-sm-4">Poisson Blending</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="2_2n3_fail/2_2_im_s.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_fail/2_2_im_s.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_fail/2_2_background.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_fail/2_2_background.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_fail/2_2_blendedip.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_fail/2_2_blendedip.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>

<h3>Part 2.2.4 - Comparing Frequency and Gradient Techniques</h3>
<p>
Frequency blending works best if you do not want the source image to change colors. However, textures are not preserved along the seam line. Poisson blending works best if you want to have a smooth transition of color and textures along the seam line. However, the coloring of the masked region may be unnatural. Mixed gradient blending works best if you want to preserve both the source and background textures in the masked region. However, sometimes the background textures may overpower the source textures, resulting in a unnatural image.
<br><br>
If the source image does not have an adequately plain background, it may not blend well with the background image. In that case where the source image has many frequencies around the edges, frequency blending would be best.
<br><br>
As shown here, the frequency blending retains the most natural coloring for the lion. The lion's mane has many frequencies and does not do as well for Poisson blending. The mixed gradient lion as some apple textures within the masked region.
</p>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-5ths">Apple</th>
<th class="col-sm-5ths">Lion</th>
<th class="col-sm-5ths">Frequency</th>
<th class="col-sm-5ths">Poisson Gradient</th>
<th class="col-sm-5ths">Mixed Gradient</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="apple.jpeg">
<img src="/assets/webp/cs194-26/3/apple.jpeg" class="img-responsive center-block" width=""></a>
</td>
<td><a href="lionface.webp">
<img src="/assets/webp/cs194-26/3/lionface.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="1_4_c/1_4_im_la.webp">
<img src="/assets/webp/cs194-26/3/1_4_c/1_4_im_la.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_al/2_2_blendedal.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_al/2_2_blendedal.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_al/2_3_blendedal.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_al/2_3_blendedal.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>

<h3>Part 2.3 - Mixed Gradients</h3>
<p>
At each pixel in the masked region, for each direction, we take the strongest gradient from either the source or background image. The result is that the masked region retains both the source's and the background's textures.
<br><br>
Mathematically, the desired pixel values $\textbf{v}$ inside the masked region can be described by
$$\textbf{v} = \text{argmin}_{\textbf{ v}} \sum_{i\in M, j\in N_i \cap M} ((v_i - v_j) - d_{ij})^2 + \sum_{i\in M, j\in N_i \cap -M} ((v_i - b_j) - d_{ij})^2$$
This is similar to the Poisson case, except:
<br>
If $abs(b_i-b_j) > abs(t_i-t_j)$ then $d_{ij} = (b_i-b_j)$ else $d_{ij} = (t_i-t_j)$.
</p>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-6">Poisson Blending</th>
<th class="col-sm-6">Mixed Gradient Blending</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="2_2n3_al/2_2_blendedal.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_al/2_2_blendedal.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_al/2_3_blendedal.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_al/2_3_blendedal.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="2_2n3_pd/2_2_blendedpd.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_pd/2_2_blendedpd.webp" class="img-responsive center-block" width=""></a>
</td>

<td><a href="2_2n3_pd/2_3_blendedpd.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_pd/2_3_blendedpd.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="2_2n3_peng/2_2_blendedip.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_peng/2_2_blendedip.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_2n3_peng/2_3_blendedip.webp">
<img src="/assets/webp/cs194-26/3/2_2n3_peng/2_3_blendedip.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>
<p>
The difference is more apparent when zoomed in. For the pencil shavings picture, the Poisson blending leaves non-textured regions that are all one tone. But for the Mixed Gradient blending, the parchment paper's texture shows throughout the image, which looks much better.
<br><br>
This technique does not work as well if textures exist in the same location on both source and background images. For example, the snow around the penguin looks good. But the snow texture overlaps with the penguin's belly.
</p>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-6">Poisson Blending</th>
<th class="col-sm-6">Mixed Gradient Blending</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="zoom/shavings1.webp">
<img src="/assets/webp/cs194-26/3/zoom/shavings1.webp" class="img-responsive center-block" width=""></a>
</td>

<td><a href="zoom/shavings2.webp">
<img src="/assets/webp/cs194-26/3/zoom/shavings2.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="zoom/peng1.webp">
<img src="/assets/webp/cs194-26/3/zoom/peng1.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="zoom/peng2.webp">
<img src="/assets/webp/cs194-26/3/zoom/peng2.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tbody>
</table>


<h3>Part 2.4 - Non-photorealistic Rendering - Simulated Cartoon Outline</h3>
<p>
Whenever there is an edge, there is a large gradient in an image. We detecting the pixels with large gradients and replacing them with another set pixel color, we can create outlines of images. Here, I experimented with using outlines of zero intensity pixels per channel, and with the mean of the source image per channel. That is, in the first case, when a gradient is above an empirical threshold, the pixel in that color channel is set to 0. In the second case, it is set to the average of that channel.
</p>
<table class="table table-striped">
<thead>
<tr>
<th class="col-sm-4">Original</th>
<th class="col-sm-4">Zero Intensity</th>
<th class="col-sm-4">Average Intensity</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="astro.webp">Astronaut
<img src="/assets/webp/cs194-26/3/astro.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_4zero/2_4_blendedpas0.webp">Astronaut
<img src="/assets/webp/cs194-26/3/2_4zero/2_4_blendedpas0.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_4mean/2_4_blendedpas1.webp">Astronaut
<img src="/assets/webp/cs194-26/3/2_4mean/2_4_blendedpas1.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="apple.jpeg">Apple
<img src="/assets/webp/cs194-26/3/apple.jpeg" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_4zero/2_4_blendedpa.webp">Apple
<img src="/assets/webp/cs194-26/3/2_4zero/2_4_blendedpa.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_4mean/2_4_blendedpa.webp">Apple
<img src="/assets/webp/cs194-26/3/2_4mean/2_4_blendedpa.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="blocks.webp">Color Blocks
<img src="/assets/webp/cs194-26/3/blocks.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_4zero/2_4_blendedpb.webp">Color Blocks
<img src="/assets/webp/cs194-26/3/2_4zero/2_4_blendedpb.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_4mean/2_4_blendedpb.webp">Color Blocks
<img src="/assets/webp/cs194-26/3/2_4mean/2_4_blendedpb.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
<tr>
<td><a href="bun.webp">Bunny in Cornell Box
<img src="/assets/webp/cs194-26/3/bun.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_4zero/2_4_blendedpb_2.webp">Bunny in Cornell Box
<img src="/assets/webp/cs194-26/3/2_4zero/2_4_blendedpb_2.webp" class="img-responsive center-block" width=""></a>
</td>
<td><a href="2_4mean/2_4_blendedpb_2.webp">Bunny in Cornell Box
<img src="/assets/webp/cs194-26/3/2_4mean/2_4_blendedpb_2.webp" class="img-responsive center-block" width=""></a>
</td>
</tr>
</tr>
</tbody>
</table>
</div>
</div>