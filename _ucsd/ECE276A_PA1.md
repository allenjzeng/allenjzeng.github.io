---
layout:      project  # Must be set to project
date:        1 January 2021
title:       Pixel Color Classification for Recycling Bin Detection
caption:     Using logistic regression and heuristics to detect blue bins.
image:
  path:      /assets/thumb/ece276a-1.webp
  srcset:
    174w:    /assets/thumb/ece276a-1.webp
description: >
  K-ary logistic regression model for predicting which pixels in an image correspond to a blue recycling bin. The model is trained on labeled data and then used to segment image data, resulting in a mask of pixels that are predicted to belong to a blue bin. The mask is then filtered using heuristics to remove noise and extract bounding boxes corresponding to the bins.
links:
  - title:   PDF
    url:     /assets/pdf/ucsd/ECE276A_PA1.pdf
featured:    false
sitemap:     true
keywords:
  - logistic regression
  - K-ary
  - blue recycling bin
  - pixel classification
  - colorspace
  - RGB
  - HSV
  - LAB
  - morphological image processing
  - segmentation mask
  - noisy mask
  - bounding box
  - connected-region
  - heuristics
  - area
  - camera view
---

Pixel Color Classification for Recycling Bin Detection
: [PDF](/assets/pdf/ucsd/ECE276A_PA1.pdf){:.no-push-state}

<object data="/assets/pdf/ucsd/ECE276A_PA1.pdf" width="100%" height="1000" type="application/pdf"></object>
