1. Introduction
1.1 Background
Image segmentation is basically a method of dividing an image into different parts or regions. The idea is to make it easier for a computer to understand what is present in an image. For example, if we have a photo of some objects, segmentation helps us to separate each object from the background.

I chose this topic because it is a very practical concept and is used in many real-life applications. In this project I have used MATLAB to perform simple object extraction from an image. MATLAB has many built-in functions which makes it easy to implement image processing without writing very complex code.

1.2 Real-Life Applications
Image segmentation is used in many fields. Some examples are:
•	In hospitals, doctors use it to detect tumours in MRI or CT scan images
•	In self-driving cars, it helps the car detect pedestrians and other vehicles
•	In security cameras, it is used for face detection
•	In agriculture, drones use it to detect diseased plants
•	In satellite images, it is used to identify forests, rivers or buildings

 
2. Problem Statement
2.1 What Problem We Are Solving
The main problem is that when we look at an image, we can easily identify different objects in it. But for a computer, the image is just a set of numbers (pixel values). So the computer cannot automatically understand where one object ends and another begins.

In this project, I am trying to solve this problem by using MATLAB to automatically detect and extract objects from an image. The program reads an image, processes it step by step, and then shows the detected objects with bounding boxes. It also extracts the largest object separately.

2.2 Aim and Objectives
Aim: To write a MATLAB program that can detect and extract objects from an image using image segmentation techniques.

Objectives:
•	To read an image in MATLAB and display it
•	To convert the image to grayscale
•	To remove noise from the image using a filter
•	To convert the image to black and white using thresholding
•	To clean the binary image using morphological operations
•	To detect and count the number of objects in the image
•	To draw bounding boxes around detected objects
•	To extract the largest object from the image

 
3. Theoretical Background
3.1 Grayscale Conversion
A normal colour image has three layers - Red, Green and Blue (called RGB). For image processing it is easier to work with a single layer image. So we convert the colour image to grayscale, which has only one layer with values from 0 (black) to 255 (white).

The formula used for conversion is:

Gray = 0.299 x R  +  0.587 x G  +  0.114 x B
In MATLAB we use the function: rgb2gray()

3.2 Gaussian Filter (Noise Removal)
When we capture an image, it sometimes has small unwanted random variations called noise. Before processing the image, we need to remove this noise. Gaussian filter is used for this purpose. It works by blurring the image slightly so that the noise gets removed.

The sigma value controls how much blurring is applied. I used sigma = 2 in this project. In MATLAB we use: imgaussfilt()

3.3 Otsu's Thresholding
Thresholding means converting a grayscale image into a black and white (binary) image. Every pixel is either made white (object) or black (background) based on its intensity value.

Otsu's method is a smart way to do this - it automatically finds the best threshold value by looking at the image histogram. We do not need to manually set the value. In MATLAB we use graythresh() to find the threshold and imbinarize() to apply it.

3.4 Morphological Operations
After thresholding, the binary image may still have some small noise spots or small holes inside the objects. Morphological operations help to clean this up.

•	imopen() - This removes small white noise spots from the image
•	imclose() - This fills small black holes that are present inside the white objects

Both these operations use a structuring element. I used a disk shape with radius 5 (strel('disk', 5)).

3.5 Object Labelling and Properties
After we get a clean binary image, we use bwlabel() to assign a unique number to each separate white region (object). Then regionprops() is used to find out properties of each object like its area, the bounding box around it, and its centroid (center point). These properties help us to draw boxes around objects and also to find the largest one.

 
4. Methodology
4.1 Image Used
I used peppers.png which is a default test image that comes with MATLAB. It is a colour image of different coloured peppers. I chose this image because it is easily available in MATLAB without needing to upload any external file, and it gives a clear output for segmentation.

4.2 Steps Followed
1.	Read the image using imread()
2.	Convert to grayscale using rgb2gray()
3.	Apply Gaussian filter to remove noise using imgaussfilt()
4.	Apply Otsu thresholding to get a binary image using graythresh() and imbinarize()
5.	Clean the binary image using imopen() and imclose()
6.	Label the objects using bwlabel() and find their properties using regionprops()
7.	Draw red bounding boxes and green centroid markers on the original image
8.	Extract the largest object and display it separately

 
5. MATLAB Implementation
5.1 MATLAB Code

clc;
clear all;
close all;

% STEP 1: Read and Display Original Image
img = imread('peppers.png');
figure(1);
imshow(img);
title('Original Image');

% STEP 2: Convert to Grayscale
if size(img, 3) == 3
    gray_img = rgb2gray(img);
else
    gray_img = img;
end
figure(2);
imshow(gray_img);
title('Grayscale Image');

% STEP 3: Noise Removal using Gaussian Filter
filtered_img = imgaussfilt(gray_img, 2);
figure(3);
imshow(filtered_img);
title('Gaussian Filtered Image');

% STEP 4: Thresholding using Otsu's Method
threshold = graythresh(filtered_img);
binary_img = imbinarize(filtered_img, threshold);
figure(4);
imshow(binary_img);
title(sprintf('Binary Image - Otsu Threshold = %.2f', threshold));

% STEP 5: Morphological Operations
se = strel('disk', 5);
cleaned = imopen(binary_img, se);
cleaned = imclose(cleaned, se);
figure(5);
imshow(cleaned);
title('Cleaned Binary Mask');

% STEP 6: Label Connected Regions
labeled = bwlabel(cleaned);
stats = regionprops(labeled, 'Area', 'BoundingBox', 'Centroid');
fprintf('Number of objects detected: %d\n', length(stats));

% STEP 7: Overlay Segmented Objects on Original Image
figure(6);
imshow(img);
hold on;
for i = 1:length(stats)
    bb = stats(i).BoundingBox;
    rectangle('Position', bb, 'EdgeColor', 'r', 'LineWidth', 2);
    plot(stats(i).Centroid(1), stats(i).Centroid(2), 'g+', ...
         'MarkerSize', 10, 'LineWidth', 2);
end
title(sprintf('Detected Objects: %d', length(stats)));
hold off;

% STEP 8: Extract and Show Largest Object
[~, idx] = max([stats.Area]);
mask = (labeled == idx);
extracted = img;
if size(img, 3) == 3
    for c = 1:3
        channel = extracted(:,:,c);
        channel(~mask) = 0;
        extracted(:,:,c) = channel;
    end
else
    extracted(~mask) = 0;
end
figure(7);
imshow(extracted);
title('Largest Segmented Object Extracted');

 
