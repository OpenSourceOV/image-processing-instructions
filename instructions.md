# Image Processing

## Requirements

Requirement | Additional information
---------|---------
Folder of captured images|
Laptop or desktop computer|With at least 4GB of RAM and a dual-core processor
ImageJ version 2| Download from [https://imagej.net/ImageJ2](https://imagej.net/Downloads)
OpenSourceOV ImageJ scripts|Download the latest version from  [https://github.com/OpenSourceOV/imagej-scripts/archive/master.zip](https://github.com/OpenSourceOV/imagej-scripts/archive/master.zip)


## Instructions

1. Follow the [ImageJ Installation Instructions](https://imagej.net/Downloads#Installation) to install ImageJ on the computer.

2. Follow the instructions at [https://github.com/OpenSourceOV/imagej-scripts](https://github.com/OpenSourceOV/imagej-scripts) to install the OSOV toolbox of scripts (don't forget to restart ImageJ).

3. Import the captured images as a stack using the Image Sequence import. Select the 'Convert to 8-bit Grayscale' option.

    When importing check the estimated memory of the imported stack displayed at the bottom of the Sequence Options window. In this case 7592.6MB, or ~7.5GB. This means the computer needs at least 7.5GB of RAM to load this entire sequence into ImageJ. But we're converting these images from 32bit colour down to 8bit which will significantly reduce the file size, so let's continue and see what happens.

    ![](./images/example-one/1.jpg)

    ![](./images/example-one/2.jpg)

4. In this example the sequence loaded and is using 1.9GB of memory (displayed at the top of the sequence window). Load the OSOV toolbox by clicking the '>>' icon to the right of the toolbar and selecting 'OSOV Toolbox'.
    
    ![](./images/example-one/3.jpg)

5. The OSOV Toolbox icon (green cube) will appear on the toolbar menu. Click the icon and choose the **Image Difference** tool.
    
    ![](./images/example-one/4.jpg)

6. Oops! Ran out of memory.

    ![](./images/example-one/5.jpg)

    If you run out of memory there are a few options:

    **Increase ImageJ memory allocation**

    The Image Difference script duplicates the stack three times and therefore requires 3 times the original stack size. It also runs some processing which requires additional memory so the total memory required to complete the operation is at least 3 or 4 times more than the original stack size. 

    From the Options menu choose **Memory & Threads...** and check the maximum memory option and increase up to ~80% of the RAM of the computer. In this case the computer is at it's maximum memory so increasing the allocated memory is not going to work.

    ![](./images/example-one/memory_option.jpg)

    **Process the sequence in sections**

    Use the 'Starting image' and 'Number of images' option in the Sequence Options dialog that appears when initiating a sequence import. For example, to import images 1-100 set the Number of images to 100 and leave Starting image at 1. To import images 200-300 set Number of images to 100 and the Starting image to 200 and so on. 

    ![](./images/example-one/sequence_options.jpg)

    **Reduce the size of the images**

    In most cases a super high resolution image is not required and you can reduce the size of the images by anywhere up to 50%. Make the reduction when importing the sequence in the Sequence Options dialog by changing the 'Scale images' option. 

    **Crop the stack**

    Crop the stack to minimise the number of pixels that require processing. In this case there are a lot of pixels around the sample that don't need processing and can be cropped out. 

    ![](./images/example-one/6.jpg)
    
    **Process a sub region**

    Run the Image Difference tool with a region selected using the rectangle tool to process only a region of the image. This can be useful if a good distribution of vein orders can be found within region.

    **Use a virtual stack**

    Instead of loading every image in the sequence into memory the images can be imported alternatively as a **Virtual Stack**. The advantage is that each image is only loaded into memory as required and then removed. The disadvantage is that scrolling through the sequence (using the scrollbar at the bottom of the stack window) becomes slow and halting as each image is loaded and unloaded.

    Select 'Use Virtual Stack' in the Sequence Options dialog to enable this option. Once the virtual stack is loaded, process the entire image or a sub region (described above) as required.

7. The Image Difference script will generate a new stack of image differences i.e. the pixel difference between each pair of images. Convert this stack to 8bit.

    ![](./images/example-one/7.jpg)

8. Apply an automatic contrast to enhance the detail. From the Image > Adjust menu select 'Brightness/Contrast...' then click the 'Auto' button. For some sequences where the differences are obvious this step isn't required.

    ![](./images/example-one/8.jpg)

    ![](./images/example-one/9.jpg)

9. Run through the stack (using the scroll bar at the bottom of the stack window) to find a stack slice with a cavitation event. 

    ![](./images/example-one/10.jpg)

10. In this case there was catastrophic embolism and most vein orders became embolised within a 5 minute interval (1 slice of the stack).

    ![](./images/example-one/10.jpg)

11. Use the Threshold tool to select as many of the pixels that represent the embolised veins. Can try different auto-thresholding methods (intermodes works well for some images). At this stage regardless of the thresholding method it won't be possible to exclude noise from the threshold. These will be removed later.

    ![](./images/example-one/11.jpg)

    ![](./images/example-one/12.jpg)

12. Run 'Analyze Particles...' from the Analyze menu and in options dialog set Size to '0-Infinity' and Circularity to '0.00-1.00' and under Show select 'Masks'. Click OK. In effect this copies all the thresholded pixels to a new stack and sets them all to black. 

    ![](./images/example-one/13.jpg)

    ![](./images/example-one/14.jpg)

13. In the new stack the noise is removed using the Remove Outliers... tool in the Process > Noise menu. Under 'Which outliers' choose 'Dark'. The default Radius and Threshold options are usually sufficient but check the 'Preview' option and increase/decrease the Radius option to determine the optimum value.

    ![](./images/example-one/15.jpg)

    ![](./images/example-one/16.jpg)

    ![](./images/example-one/17.jpg)


14. Remove remaining noise and artifacts

    Outlier removal will remove almost all of the randomly distributed noise but there will be groups of non-random pixels remaining that must be removed manually. These non-random groups are typically the result of leaf shrinkage, sample movement (e.g. being knocked), creatures walking across the sample during a capture leaving a sci-fi monster blur across the image. If the voltage supply to the LED lights was unregulated and substantial voltage ripple was present (an artifact of AC power converted to DC) it may be visible in the sequence as a series of bands that scroll from the top to bottom.

    There are four tools in the OSOV Toolbox to expedite the process of manual cleaning. 

    * **Clear Slice** - clears everything from the current stack slice (image)
    * **Clear Slices** - clears everything from slices in the slice number range provided. The slice number is displayed at the top of the stack window.
    * **Save Slices** - will clear all slices *except* the slices specified in the comma-separated list.
    * **Clear Outside** - will clear everything outside the area currently selected. This tool is best used with the Selection Brush set to ~100 pixels. Select all the events on the image then use Clear Outside to remove everything else. Note this works differently to the ImageJ Clear Outside function under the Edit menu.

    **Synchronise stacks**

    The first thing to do is arrange all three windows - the original stack, the image difference stack and the extracted particles stack (the one to be cleaned) - and synchronise them so as you progress through one they all progress.

    ![](./images/example-one/18.jpg)
    
    ![](./images/example-one/19.jpg)
    
    **Work through each slice**

    Starting at the first slice work your way through the stack checking the difference stack to ensure that you're only removing artifacts and noise, not embolism events. To streamline the workflow further set the four cleaning tools as shortcuts using the Plugins > Shortcuts > Add Shortcut... function.
    
    ![](./images/example-one/21.jpg)

15. Measure the total area of embolism per slice.

    **Set the scale to 1:1** - because we will be using relative area.

    ![](./images/example-one/27.jpg)
    
    ![](./images/example-one/28.jpg)

    **Set area and threshold options in Measurements options** 
    
    Set Area as a measurement and ensure Limit to Threshold option is selected.

    ![](./images/example-one/29.jpg)

    ![](./images/example-one/30.jpg)

    **Threshold the black pixels**

    Using the Image > Adjust > Threshold... tool select all black pixels (adjust the sliders until all the pixels are red highlighted)

    ![](./images/example-one/26.jpg)

    **Measure**

    From the OSOV Toolbox select 'Measure Stack'

    ![](./images/example-one/31.jpg)

16. Save the Results to a file for processing

    ![](./images/example-one/33.jpg)

17. Done!





    
