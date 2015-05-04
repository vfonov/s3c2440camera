# Introduction #

Video For Linux (V4L) is an API that provides unified access to various video capturing devices, such as TV tuners, USB web cameras, etc

we will cover usage of the video capture interface, provided by the /dev/video character device.

# Initializing and registering a V4L2 driver #

**Some important data structures**

To register V4L2 device it is necessary to fill three main structures:

  * video\_device,
  * v4l2\_file\_operations,
  * v4l2\_ioctl\_ops

Let's look closer at initializing each of these structures,

```

static struct v4l2_file_operations s3c2410camif_fops = {
        .owner = THIS_MODULE,
        .open = camif_open,
        .release = camif_release,
        .read = camif_read,
        .ioctl = video_ioctl2,
        .mmap = NULL,//TODO: implement this camif_mmap
        .poll = camif_poll,
};

static const struct v4l2_ioctl_ops s3c2410camif_ioctl_ops = {
        .vidioc_querycap         = s3c2410camif_querycap,
        .vidioc_enum_fmt_vid_cap = s3c2410camif_enum_fmt_vid_cap,
        .vidioc_g_fmt_vid_cap    = s3c2410camif_g_fmt_vid_cap,
        .vidioc_s_fmt_vid_cap    = s3c2410camif_s_fmt_vid_cap,
        .vidioc_g_ctrl           = s3c2410camif_vidioc_g_ctrl,
        .vidioc_s_ctrl           = s3c2410camif_vidioc_s_ctrl,
        .vidioc_enum_input       = s3c2410camif_vidioc_enum_input,
        .vidioc_queryctrl        = s3c2410camif_vidioc_queryctrl,
};

static struct video_device s3c2410camif_v4l_template = {
        .name = "s3c2410camif Camera",
        .fops = &s3c2410camif_fops,
        .ioctl_ops = & s3c2410camif_ioctl_ops,
        .release = video_device_release,
};

```

Let's look in more detail at the purposes of some fields of these structures.

The video\_device structure is declared in the <media/v4l2-dev.h> file. It contains general information about the video device. The fops and ioctl\_ops fields point at the corresponding structures v4l2\_file\_operations and v4l2\_ioctl\_ops.

The release field contains a pointer to the function that will be called when the video device is removed from system. In this case, this field points at the standard video\_device\_release function, which just releases memory allocated for the video\_device structure.

The file\_operations structure is defined in the <linux/fs.h> file, and is an important kernel structure. It contains pointers to functions, each of which implements a corresponding operation, or to NULL.

The capture driver defines handlers of the main system calls for the device file /dev/video: open, release, read, poll, mmap, ioctl. A specific implementation of these handlers will be considered later in the article.

It is necessary to mention that the video\_ioctl2 standard function is used as a handler for the ioctl call. This function performs the initial processing of an ioctl call, analyzing the command's code and calling a corresponding handler using pointers to functions specified in the v4l2\_ioctl\_ops structure. You can specify your own function for processing ioctl, but in that case, you will have to implement code for command analysis independently. Developers of V4L2 recommend using video\_ioctl2, in order to decrease the probabilities of errors in driver code.

The v4l2\_ioctl\_ops structure stores pointers to handlers of V4L2 commands transmitted by means of an ioctl system call. This structure is declared in <media/v4l2-ioctl.h> file.

# Registering a video device #

The kernel uses the video\_device structure for describing video device. This structure can be created both statically and dynamically, by using the following function:

**struct video\_device**video\_device\_alloc(void);


To use video\_device\_alloc in your code, you have to include <media/v4l2-common.h>.

Then for releasing memory occupied by the structure, make the following call:

**void video\_device\_release(struct video\_device**vfd);


A video device is registered by the following function:

**int video\_register\_device(struct video\_device**vfd, int type, int nr);


The vfd parameter in the examples above is the pointer to the video device structure that you want to register.

type parameter defines the type of registered device. The following values define device types:

```
VFL_TYPE_GRABBER -- video capturing device
VFL_TYPE_VTX -- teletext device
VFL_TYPE_VBI -- VBI device
VFL_TYPE_RADIO -- radio card
```

The nr parameter sets the number of the registered device: 0 for /dev/video0, 1 for /dev/video1, etc. If you want to assign the first available number, then specify -1 for this parameter.

To remove a video device from system, you have to call the following function:

**void video\_unregister\_device(struct video\_device**vfd);


```

       memset(cam, 0, sizeof(s3c2440camif_dev));
       cam->video_dev = video_device_alloc();
       if (cam->video_dev == NULL)
                  return -ENOMEM;
       *(cam->video_dev) = s3c2410camif_v4l_template;

       cam->sensor_op = &s3c2440camif_sensor_if;

       cam->video_dev->minor = -1;

        /* register v4l device */
        if (video_register_device(cam->video_dev, VFL_TYPE_GRABBER, video_nr) == -1)
```

# Sensor operations #

```
s3c2440camif_sensor_operations s3c2440camif_sensor_if =
{
        .init=s3c2440_ov9650_init,
        .cleanup=s3c2440_ov9650_cleanup,
        .set_format=s3c2440_ov9650_set_format,
        .get_format=s3c2440_ov9650_get_format,
        .get_largest_format=s3c2440_ov9650_get_largest_format,
        .poweron=ov9650_poweron,
        .poweroff=ov9650_poweroff,
        
        .get_auto_exposure=s3c2440_ov9650_get_auto_exposure,
        .get_auto_gain=s3c2440_ov9650_get_auto_gain,
        .get_auto_wb=s3c2440_ov9650_get_auto_wb,
        .get_exposure=s3c2440_ov9650_get_exposure,
        .get_gain=s3c2440_ov9650_get_gain,
        
        .set_auto_exposure=s3c2440_ov9650_set_auto_exposure,
        .set_auto_gain=s3c2440_ov9650_set_auto_gain,
        .set_auto_wb=s3c2440_ov9650_set_auto_wb,
        .set_exposure=s3c2440_ov9650_set_exposure,
        .set_gain=s3c2440_ov9650_set_gain,
};
```

# IOCTL VS Function pointer in driver #

|VIDIOC\_QUERYCAP |vidioc\_querycap|
|:----------------|:---------------|
|VIDIOC\_ENUMINPUT|vidioc\_enum\_input|
|VIDIOC\_S\_FMT |vidioc\_s\_fmt\_vid\_cap|
|VIDIOC\_G\_FMT | vidioc\_g\_fmt\_vid\_cap|
|VIDIOC\_ENUM\_FMT | vidioc\_enum\_fmt\_vid\_cap|
|VIDIOC\_QUERYCTRL|vidioc\_queryctrl|
|VIDIOC\_G\_CTRL | vidioc\_g\_ctrl |
|VIDIOC\_S\_CTRL | vidioc\_s\_ctrl |