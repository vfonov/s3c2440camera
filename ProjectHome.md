Implementation of Video 4 Linux interface for embedded systems based on Samsung S3C2440 CPU


## Building & Installing ##

  * obtain ARM-Linux GCC 4.3.2  and Linux 2.6.29 from [FriendlyARM](http://www.friendlyarm.net/downloads)
  * setup environment variables CC,CPP  etc, use [this](http://s3c2440camera.googlecode.com/files/ARM-Linux-GCC-4.3.2.sh) as a reference
  * compile linux kernel , without S3C2440\_CAMERA ( find line `CONFIG_S3C2440_CAMERA=y` and replace with `CONFIG_S3C2440_CAMERA=m`)
  * install new kernel
  * compile new camera module , `make KERNELDIR=<whenever you have linux kernel>`
  * (optional) compile test program `make test_capture`
  * (optional) compile tiff capture program `make tiff_capture TIFFDIR=<where you have libtiff>`
  * copy kernel module s3c2440camera.ko to mini2440
  * load kernel module `insmod s3c2440camera.ko `
  * run the test capture program: `./test_capture /dev/video0 <width> <height> <filename base> [number of frames]`
  * Output of a sample session:
```
[root@FriendlyARM /] insmod s3c2440camera.ko 
Loading OV9650 driver.........
SCCB address 0x60, manufacture ID 0x7FA2, expect 0x7FA2
SCCB address 0x60, product ID 0x9652, expect 0x9650
OV9650 setting format 1  640x480 126 parameters!
ov9650 init done!
[root@FriendlyARM /]# ./test_capture /dev/video0 640 480 test 10  
OV9650 setting format 0  1280x1024 125 parameters!
OV9650 setting format 1  640x480 126 parameters!
Goint to capture frames 640 x 480 format: 422P framesize: 614400
About to capture 10 images to test
**********
29.886700 fps, Saving collectected frames...
0	1	2	3	4	5	6	7	8	9	
```

## Prebuild binaries ##

  * Prebuild binaries based on [r1](https://code.google.com/p/s3c2440camera/source/detail?r=1) ara available: [kernel](http://s3c2440camera.googlecode.com/files/linux-2.6.29.4-FriendlyARM-zImage) , [s3c2440camera.ko](http://s3c2440camera.googlecode.com/files/s3c2440camera.ko)
  * mjpg-streamer adopted for mini2440 : [mjpg-streamer-mini2440](http://code.google.com/p/mjpg-streamer-mini2440/)

