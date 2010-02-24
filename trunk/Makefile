# If KERNELRELEASE is defined, we've been invoked from the
# kernel build system and can use its language.

# or specify your own in command line
KERNELDIR ?=../linux-2.6.32.2
# where libtiff is compiled, only needed for tiff_capture
TIFFDIR ?= ../tiff-3.9.2

ifneq ($(KERNELRELEASE),)
	obj-m := s3c2440camera.o
	s3c2440camera-objs      :=      s3c2440_ov9650.o sccb.o s3c2440camif.o

# Otherwise we were called directly from the command
# line; invoke the kernel build system.
else
	PWD  := $(shell pwd)

default:
	$(MAKE) -C $(KERNELDIR) M=$(PWD) ARCH=arm modules

test_capture: test_capture.c
	$(CC) -Wall -std=gnu99  -O3 test_capture.c -o test_capture 
#-I$(KERNELDIR)/include

set_parameters: set_parameters.c
	$(CC) -Wall -std=gnu99  -O3 set_parameters.c -o set_parameters 

tiff_capture: tiff_capture.c
	$(CC) -Wall -std=gnu99 -I$(TIFFDIR)/libtiff -L$(TIFFDIR)/libtiff/.libs -O3 tiff_capture.c -o tiff_capture -ltiff -lz -lm  
#-I$(KERNELDIR)/include

clean:
	rm -f s3c2440camera.mod.o  s3c2440camera.o  s3c2440camif.o  s3c2440_ov9650.o  sccb.o	s3c2440camera.ko s3c2440camera.mod.c  test_capture tiff_capture 

tar:
	tar czf s3c2440camera.tar.gz *.c *.h Makefile 
endif
