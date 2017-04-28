## ME 592 Assignment 1

### This file contains comments and results for Assignment1. Source codes can be found in 'Assignment1.ipynb'.

---

[//]: # (Image References)
[P1_1]: ./output_images/P1_1.png
[P1_2]: ./output_images/P1_2.png
[P1_3]: ./output_images/P1_3.png
[P2_1_hist]: ./output_images/P2_1_hist.png
[P2_2_hist]: ./output_images/P2_2_hist.png
[P2_2_s_20_130]: ./output_images/P2_2_s_20_130.png
[P2_1_l_70_110]: ./output_images/P2_1_l_70_110.png
[P3]: ./output_images/P3.png
[P4_scale_100]: ./output_images/P4_scale_100.png
[P4_scale_200]: ./output_images/P4_scale_200.png
[P4_scale_mean]: ./output_images/P4_scale_mean.png
[P5_channel_histograms]: ./output_images/P5_channel_histograms.png
[P5_pathes_white]: ./output_images/P5_pathes_white.png
[P5_pathes]: ./output_images/P5_pathes.png
[P7_output1_segmentation]: ./output_images/P7_output1_segmentation.png
[P7_output1_zoom]: ./output_images/P7_output1_zoom.png
[P7_output1]: ./output_images/P7_output1.png
[P7_output2_filtered_segmentation]: ./output_images/P7_output2_filtered_segmentation.png
[P7_output2_filtered]: ./output_images/P7_output2_filtered.png
[P7_output2]: ./output_images/P7_output2.png
[P7_running_average]: ./output_images/P7_running_average.png
[P7_signals]: ./output_images/P7_signals.png
[P8_data_histogram]: ./output_images/P8_data_histogram.png
[P8_data_visualize_zoom]: ./output_images/P8_data_visualize_zoom.png
[P8_data_visualize]: ./output_images/P8_data_visualize.png
[P8_distances]: ./output_images/P8_distances.png

#### P1 Basic Image Processing

The difference between the first image and the target image is just: the pixel intensities in the first image are scaled down from the target image with a fixed rate. So the frist image looks darker. To fix this, I can either scale the pixel intensities back or just simply display the frist image in gray scale. Results shown below:
![alt text][P1_1]

The second image was added random noises in. I can reduce the noise by smoothing it. Different kernal size were tried using both guassian blur and median blur. The results are shown below. Median blur with kernal size = 7 gave the best results and histogram equalization can further sharpen it a little.
![alt text][P1_2]

The third image has both noise and dark shading. Combine the methods used for image1 and image2 provide the following results:
![alt text][P1_3]

#### P2 Background subtraction

As being aware that binary thresholding method is far from enough to get the sample results provided in the given PPT, still binary thresholding method was used as instructed.

Instead of gradient mask, only single channel color intensity mask is considered. Total 9 channels in RGB, HLS and YUV color spaces are explored. A interavtive experimental tool is created to help select the threshold. The results are shown below:

Histogram of 9 color channels of image1:
![alt text][P2_1_hist]

Result after thresholding on L channel with range (70,110):
![alt text][P2_1_l_70_110]

Histogram of 9 color channels of image2:
![alt text][P2_2_hist]

Result after thresholding on S channel with range (20,130):
![alt text][P2_2_s_20_130]

#### P3 Metric in image space

MSE and SSIM are selected as distance mesure for this problem. Lower value in MSE and higher value in SSIM meams higher similarity. The distance between each feature map and the input are plotted below, MSE and SSIM measures the similarity similarly.
![alt text][P3]

#### P4 Shading correction

I used `np.mean(Imax.astype("float")-Imin.astype("float"))` as the constant in the equation described [here](http://www.mif.vu.lt/atpazinimas/dip/FIP/fip-Shading-2.html)
Then SSIMs is calcualted and are plotted below, all SSIMs shown in the figure are between the corresponding image and the groud truth image:
![alt text][P4_scale_mean]

#### P5 Image pre-processing and transformation

5000 50by50 patches are randomly collected from the provided 8 images. Some of them are randomly chosen and shown below:
![alt text][P5_pathes]

After prewhittening (equalizing the variance), the patches look like this:
![alt text][P5_pathes_white]

And channel by channel distribution of original patches and whittened patches look like:
![alt text][P5_channel_histograms]
As shown in the figure above, the channel distributions are balanced after whittening. Since the variance are equalized in the prewhittened data, it can help in the latter data analysis process.

#### P7 Time series pre-processing

First the data are loaded in and a small piece is visualized below:
![alt text][P7_signals]
As shown in the above figure, the plotted segment of output1 is basically consist of two components. After applying a low pass filter (hyperparameters are manually tuned), the filtered output2 looks clean.

The entire ouput1 is shown below:
![alt text][P7_output1]
The figure indicates that the output1 probably has three stages: stage1, stage2 and a malfunction/missing stage, stage 3.

If we zoom in to take a close look at stage 1 (left) and stage 2 (right):
![alt text][P7_output1_zoom]
we can see stage2 looks just like stage1 with the lower frequency component removed.

For stage segmentation, I used a sliding window techique with window size = 1000 and strike length = 1000 (no overlay). `Max`, `std` and the number of dominent signal components (expect stage1 has 2 dominent components and stage2 has 1) are extracted from each window. then `if _max>1.5 and _std>1 and _numcompo == 2`, I segment the window as stage1 (coded as 1), `if _max<1.1 and _std<1 and _numcompo == 1`, I segment the window as stage2 (coded as -1), otherwise the window is stage3 (coded as 0). The segmentation result on output1 is shown below: 
![alt text][P7_output1_segmentation]
THe segmentation looks good.

Then for noise output 2:
![alt text][P7_output2]

I apply the low pass filter and the filtered data looks:
![alt text][P7_output2_filtered]

After applying the same segmentation strategy, the results are like:
![alt text][P7_output2_filtered_segmentation]
For this noise case, the simple segmentation strategy doesn't work well for stage 1, it should be caused by the fact that the code for finding the dominent components are not robust enough to noise.

The figure below shows the running average calculated on the sliding windows:
![alt text][P7_running_average]
And the low maginitue and the flat trend shows both the two systems are stationary.

#### P8 Metrics in Time series space

First data are loaded in and visualized:
![alt text][P8_data_visualize]

Here is a zoomed in view:
![alt text][P8_data_visualize_zoom]

Three distances are calulated:
* euclidean distance between the raw data vectors (262800 by 1)
* KL divergence between the histograms of the raw data vectors.
* euclidean distance between the wavelet transformation matrix (use `signal.ricker` function and 50 different widths) of the raw data vectors (262800 by 50)

Here shows the histograms (use '(min,max)' as range and 1000 bins) which are a discretized space of the raw data:
![alt text][P8_data_histogram]

And finally here are distances of all the energy utilities to the main power:
![alt text][P8_distances]
The euclidean distance of raw data is consistant with the euclidean distance of wavelet-transformed data. The KL divergence shows some different behavior because I intentionally used (min,max) as the histogram range which is a adaptive range. And the KL divergence matches with the histogram plot.

