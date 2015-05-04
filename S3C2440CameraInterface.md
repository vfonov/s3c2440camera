# Introduction #
**CAMIF (CAMera InterFace)** within the S3C2440A consists of 7 parts
  * pattern mux
  * capturing unit
  * preview scaler
  * codec scaler
  * preview DMA
  * codec DMA
  * SFR.

**The CAMIF supports ITU-R BT.601/656 YCbCr 8-bit standard.**

**Maximum input size is 4096x4096 pixels (2048x2048 pixels for scaling) and two scalers exist.**

**Preview scaler is dedicated to generate smaller size image like PIP (Picture In Picture)**

**codec scaler is dedicated to generate codec useful image like plane type YCbCr 4:2:0 or 4:2:2.**

**Two master DMAs can do mirror and rotate the captured image for mobile environments.**

**TWO DMA PATHS**

CAMIF has 2 DMA paths. P-path (Preview path) and C-path (Codec path) are separated from each other on the
AHB bus.


**In view of the system bus, both the paths are independent.**

**The P-path stores the RGB image data intomemory for PIP.**

**The C-path stores the YCbCr 4:2:0 or 4:2:2 image data into memory for Codec as MPEG-4, H.263,etc.**


**FRAME MEMORY HIRERARCHY**

Frame memories consist of four ping-pong memories for each of P and C paths .

C-path ping-pong memories have three element memories – luminance Y, chrominance Cb, and chrominance Cr.

If AHB-bus traffic is not enough for the DMA operation to complete during one horizontal line period, it may lead to malfunctioning

**MEMORY STORING METHOD**

The little-endian method in codec path is used to store in the frame memory. The pixels are stored from LSB to MSB
side.

AHB bus carries 32-bit word data. So, CAMIF makes each of the Y-Cb-Cr words in little-endian style.

For preview path, two different formats exist. One pixel (Color 1 pixel) is one word for RGB 24-bit format. Otherwise,
two pixels are one word for RGB 16-bit format.

# Registers #
Register :CIGCTRL
|CIGCTRL|BIT|Description|
|:------|:--|:----------|
|SwRst |[31](31.md) |Camera interface software reset |
|CamRst |[30](30.md)| External camera processor reset or power down |
|Reserved| [29](29.md)| This bit is reserved and the value must be 1. |

```
/* software reset camera interface. */
static void __inline__ soft_reset_camif(void)
{       
        u32 cigctrl;
        
        cigctrl = (1<<31)|(1<<29);
        iowrite32(cigctrl, S3C244X_CIGCTRL);
        mdelay(10);

        cigctrl = (1<<29);
        iowrite32(cigctrl, S3C244X_CIGCTRL);
        mdelay(10);
}

/* software reset camera interface. */
static void __inline__ hw_reset_camif(void)
{
        u32 cigctrl;

        cigctrl = (1<<30)|(1<<29);
        iowrite32(cigctrl, S3C244X_CIGCTRL);
        mdelay(10);

        cigctrl = (1<<29);
        iowrite32(cigctrl, S3C244X_CIGCTRL);
        mdelay(10);

}
```


**Register:CISRCFMT[source format register](Input.md)**

```
/* update CISRCFMT only. */
static void __inline__ update_source_fmt_regs(s3c2440camif_dev * pdev)
{
        u32 cisrcfmt;

        cisrcfmt = (1<<31)                                      // ITU-R BT.601 YCbCr 8-bit mode
                                |(0<<30)                                // CB,Cr value offset cntrol for YCbCr
                                |(pdev->sensor.width<<16)       // source image width
//                              |(0<<14)                                // input order is YCbYCr
//                              |(1<<14)                                // input order is YCrYCb
                                |(2<<14)                                // input order is CbYCrY
//                              |(3<<14)                                // input order is CrYCbY
                                |(pdev->sensor.height<<0);      // source image height

        iowrite32(cisrcfmt, S3C244X_CISRCFMT);
}
```

**CICOTRGFMT Target image format of codec DMA**


```
static void __inline__ update_target_fmt_regs(s3c2440camif_dev * pdev)

 /* CICOTRGFMT. */
  cicrtrgfmt =  (1<<31) //use input YCbCr:422 mode
               |(1<<30) //use output YCbCr:422 mode
               |(pdev->v2f.fmt.pix.width<<16)   // horizontal pixel number of target image
               |(0<<14)            // don't mirror or rotation.
               |(pdev->v2f.fmt.pix.height<<0); // vertical pixel number of target image

  iowrite32(cicrtrgfmt, S3C244X_CICOTRGFMT);
```