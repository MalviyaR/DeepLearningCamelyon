# Deep Learning Pipeline for Camelyon 2016 dataset

    Weizhe Li, Weijie Chen


## 1. Set up deep learning environment.

        – Setup Python Environment
        – Install CUDA
        – Install tensorflow

## 2. ASAP installation and image display

– Compare the ASAP and OpenSlide: ASAP doesn’t have detailed manual to describe its commands; OpenSlide has a much better document for its commands. ASAP has a GUI; OpenSlide doesn’t   
   
## 3. Mask file generation, annotation visulization.

Mask file is the ground truth for model training. Mask file has the exact same dimensions as its corresponding WSI image.  Mask file is a binary file with normal tissue coded as ‘0’ and tumor tissue coded as ‘1’ for each corresponding pixel of WSI image. 
However, the code provided by the organizer is misleading. The mask file generated by the code from their paper is all ‘0’; then another piece of code suggested generated mask file that can be open only by ASAP GUI, not by its command line and OpenSlide.
Time consuming

## 4. image manipulation, tumor and normal tissue patch extraction

 	-- To reduce computation, the blank regions (no tissue) on slide will be excluded. 
  		Tissue region segmentation (Otsu’s method of foreground segmentation)

-- tumor regions and normal regions are distinguished by mask file

##5. patch extraction


-- Step 1 : Randomly extract patches (256 x 256) on the tissue region at the level of 40x
               
Tumor slide : 1K positive and 1K negative from each slide
            	Normal slide: 1K negative from each slide
            Threshold setup based on greyscale file;
	    Threshold setup based on HSV color space;

-- Step 2 : Crop 224x224 patches and conduct image augmentation
            color normalization
	    adding color noise
	    flip

-- Image Preparation

--Image normalization
	Images show variety of brightness and colors.
--Color space switch to HSV
		
Make image formatted according to tensorflow

## 6. Feed neural network (FCN, GoogleNet, ResNet)
	
	FCN:

	Lambda, Normalize input (x / 255.0 - 0.5), outputs 256x256x3 
0. Convolution1, 5 x 5 kernel, stride 2, outputs 128x128x100 
1. Maxpooling1, 2 x 2 window, stride 2, outputs 64x64x100 
2. Convolution2, 5 x 5 kernel, stride 2, outputs 32x32x200 
3. Maxpooling2, 2 x 2 window, stride 2, outputs 16x16x200 
4. Convolution3, 3 x 3 kernel, stride 1, outputs 16x16x300 
5. Convolution4, 3 x 3 kernel, stride 1, outputs 16x16x300 
6. Dropout, 0.1 rate 
7. Convolution5, 1x1 kernel, stride 1, outputs 16x16x2 
8. Deconvolution, 31 x 31 kernel, stride 16, outputs 256x256x2 


Tuning parameters: activation method, padding, strides, optimizer

Feed neural network 

Save the model for prediction

## 7. Make predictions and construct heatmaps

Test images were divided into non-overlapping small patches; each patch will get a predicted image for each pixel assigned by probability.
Heatmap is a way to display the probability
Put all the patches together and get prediction for the whole slide. 

    
## 8. tumor position detection and classification

Feature extraction: area of tumor, number of tumor regions, diameter of tumor region, etc. 
random forest vs Support Vector Machine (svm)

## 9. find the tumor region
   	 
tumor segmentation based on heatmap

## 10. increase the accurary

If the false positive is high:

Extract additional training patches from false positive regions

Training again (model-2)

Combine Model-1 and Model-2