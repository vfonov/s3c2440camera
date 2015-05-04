# Introduction #

The FriendlyARM Mini2440 is a single board computer based on a Samsung S3C2440 ARM9

<img src='http://s3c2440camera.googlecode.com/files/mini2440-35.jpg' height='300px' width='300px'>


<h1>Details</h1>

<ul><li>Samsung S3C2440A (ARM920T), 400MHz, max. 533Mhz<br>
</li><li>64 MB of SDRAM, 32bit Bus, 100MHz Clock<br>
</li><li>64MByte or 128MByte Nand Flash<br>
</li><li>Debug Port: COM0 + JTAG + USB Slave<br>
</li><li>10/100M MB Ethernet, RJ-45 interfaces</li></ul>

<img src='http://s3c2440camera.googlecode.com/files/mini2440_des.jpg' height='300px' width='400px'>

<h1>Interface</h1>

The <b>CAMIF</b>, also the Camera Interface block is the hardware block that interfaces with different image sensors and provides a standard output that can be used for subsequent image processing.<br>
<br>
A typical <b>Camera Interface</b> would support at least a parallel interface although these days many camera interfaces are beginning to support the Mobile Industry Processor Interface MIPI CSI interface.<br>
<br>
The camera interface's parallel interface consists of the following lines :-<br>
<br>
<b>8 to 12 bits parallel data line</b>
:These are parallel data lines that carry pixel data. The data transmitted on these lines change with every Pixel Clock (PCLK).<br>
<br>
<b>Horizontal Sync (HSYNC)</b>
:This is a special signal that goes from the camera sensor or ISP to the camera interface. An HSYNC indicates that one line of the frame is transmitted.<br>
<br>
<b>Vertical Sync (VSYNC)</b>
:This signal is transmitted after the entire frame is transferred. This signal is often a way to indicate that one entire frame is transmitted.<br>
<br>
<b>Pixel Clock (PCLK)</b>
:This is the pixel clock and it would change on every pixel.<br>
<br>
<b>NOTE</b>: The above lines are all treated as input lines to the Camera Interface hardware.<br>
<br>
<h2>Image Sensor</h2>

<b>An image sensor is an electrical device that converts an light (photons) to electronic signal form</b>

<b>OV9650 1.3 Mega Pixels CMOS Camera</b> for S3C2440/S3C6410  SOC Based Development Boards<br>
<br>
<img src='http://s3c2440camera.googlecode.com/files/ov9650.jpg' height='200px' width='200px'>

<img src='http://s3c2440camera.googlecode.com/files/camera_interface.png' height='300px' width='300px'>

<h1>Pixel Formats</h1>

There are different methods for representing color and intensity information in a video image. The video format that a file uses to store this information is also known as the <b>pixel format</b>

There are two major types of pixel formats. They are classified as <b>YUV</b> and <b>RGB</b> pixel formats.<br>
<br>
<h2>YUV Pixel Format</h2>
YUV Color System is a color-encoding scheme for natural pictures in which the luminance (the black-and-white component of a video signal that controls the light intensity) and chrominance (the color component of the video signal) are separate. The human eye is less sensitive to color variations than to intensity variations, so YUV allows the encoding of luminance (Y) information at full bandwidth and chrominance (UV) information at half bandwidth.<br>
<br>
YUV formats are subdivided into two more groups: packed and planar. In the packed format, the Y, U, and V components are stored in a single array. The three components packed into what is known as a macropixel (two pixels stored in one unsigned integer value). Conversely, the planar format stores these components in three separate arrays and combines the planes to form the image.<br>
<br>
The variations in the different YUV samples are based on how data is sampled, both in the horizontal and vertical directions. The horizontal subsampling interval describes how frequently across a line that a sample of that component is taken, and the vertical interval describes on which lines samples are taken. For example, if the format has a horizontal subsampling period of 2 for both the U and V components, it indicates that U and V samples are taken for every second pixel across a line. If the vertical subsampling period is 1, it indicates that U and V samples are taken on each line of the image.<br>
<br>
<h3>UYVY ( Y422 and UYNV )</h3>

UYVY is probably the most popular of the various YUV 4:2:2 formats. It is output as the format of choice by the Radius Cinepak codec and is often the second choice of software MPEG codecs after YV12.<br>
<br>
Y422 and UYNV appear to be direct equivalents to the original UYVY.<br>
<br>
<img src='http://s3c2440camera.googlecode.com/files/uyvy.gif' width='600px'>

Effective bits per pixel : 16<br>
<br>
<h2>RGB Pixel Format</h2>
The primary colors in color video are red, green, and blue (RGB). RGB is often used to describe a type of video color-recording scheme and the type of equipment that uses it. It also describes a type of computer color display output signal comprising separately controllable red, green, and blue signals (as opposed to composite video, in which signals are combined before output). RGB monitors typically offer higher resolution than composite monitors.<br>
<br>
<h2>OV9650</h2>

<b>Resolution</b>

<table><thead><th> SXGA  </th><th> 1280×1024 </th></thead><tbody>
<tr><td> VGA   </td><td> 640x480   </td></tr>
<tr><td> QVGA  </td><td> 320x240   </td></tr></tbody></table>

<b>Pixel format</b>

RGB (GRB 4:2:2), YUV (4:2:2) and YCbCr (4:2:2) formats<br>
<br>
<b>RESET and PWDN</b>

RESET clears all registers and resets them to their default values. Active high, internal pull-down resistor<br>
<br>
Power Down Mode Selection - active high, internal pull-down resistor.<br>
<br>
0:Normal mode<br>
<br>
1:Power down mode