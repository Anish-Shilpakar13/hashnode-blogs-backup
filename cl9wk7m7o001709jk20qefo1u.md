---
title: "Remove Images Background Using A Single Command"
seoTitle: "How to remove background from images using Python?"
seoDescription: "Rembg is a Python tool that can be used for removing background from images easily and quickly and can remove background with single command."
datePublished: Mon Oct 31 2022 09:09:12 GMT+0000 (Coordinated Universal Time)
cuid: cl9wk7m7o001709jk20qefo1u
slug: remove-images-background-using-a-single-command
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1667206626569/XeWxOslFG.jpg
tags: ai, code, python, command-line

---

Recently, I was working on a project where I need to remove background of images. I knew I could do it using tools like Photoshop or any other photo editors, but I wanted a more efficient way of doing it. After doing some research I found an interesting package in python which allowed me to remove background from images, so today I am writing to share about this wonderful python package named **Rembg** which can be used to remove background from images more quickly and efficiently. The interesting thing about this tool is that it uses Artificial Intelligence for accurately removing background of images. In this tutorial, I will present you a guide to installing and using this tool easily.

## Installation
It is really easy to install rembg. You can use pip to install this package by using the following command:
``` 
pip install rembg
```
*Note: You may get some installation errors with Python 3.10+ so use Python 3.85 to install this package*

## Working
I will show you different ways you can use this package to remove the background of images.

### Method 1: Using CLI
This is the simplest way to use rembg to remove the background of images. You just need to type this single command in your CLI.
```
rembg i path/to/input_image path/to/output_image
```
Example:
I will consider this image from Pexels.com to remove the background.
![Input Image](https://images.pexels.com/photos/9775664/pexels-photo-9775664.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1)

I have saved this file as input.jpg in my local folder, then I will open my command line in the same folder and type the following command:
```
rembg i input.jpg output.jpg
```
This will now create a new file called output.jpg in the same folder and this is how the output looks like:
![output.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1667204320427/H9r8oVM4m.jpg align="left")
You can see the output is pretty neat.

You can also directly remove the background of all the images in a folder using the following command
```
rembg p path/to/input_folder path/to/output_folder
```
Example:
For this I downloaded some images from pexels.com and kept in a folder called bg as shown below. Also, no-bg will contain images after removing background.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1667205094483/5N1YoAT84.png align="left")

Images before transformation

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1667205114093/TkHYEu60L.png align="left")

Command
```
rembg p bg no-bg
```
Images after transformation

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1667205257794/qE1AQVifx.png align="left")
Image before transformation

![pexels-kübra-arslaner-13722193.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1667205649354/uh3_WXhZq.jpg align="left")

Image after transformation
![pexels-kübra-arslaner-13722193.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1667205654668/wwu_6heYD.png align="left")

### Method 2: Writing a Python Program
Now let's write a short Python program that will do the same task.
```
# Program name: remove-background.py
# imports
from rembg import remove 
from PIL import Image 
# Input and Output Image path
input_path = input('Enter Input Image Path: ')
output_path = input('Enter the Output Image Path: ')
# Using Pillow to open the input image
input_image = Image.open(input_path)
output_image = remove(input_image)
output_image.save(output_path)
print('Transformation Done')
```
Running this program
```
python remove-background.py
```
Output

![cv1.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1667206745846/_9SBQLqb9.jpg align="left")

## Conclusion

Hence, after this tutorial, you can now easily remove the background from the images without need of any other external tool and by using a single command. If you want to learn more about this rembg package, check out the resources below. 

## Resources 
1. https://pypi.org/project/rembg/ 
2. https://github.com/danielgatis/rembg


