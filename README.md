# What is this?
Set of experiments revisiting the **Raspberry Pi Model B 512MB** in 2025 to see what it is capable. Since desktop environment is too heavy for it, my focus will be using a thing called **framebuffer**, but in a headless/console mode only, which still allow us to use the GPU and graphics.

# Raspberry Pi Model B rev 2 512MB
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/745bd5f6-3090-40f0-906b-0cd78af5f0d5" />

* SoC: Broadcom BCM2835
* CPU: Single-core ARM1176JZF-S 32-bit CPU running at 700 MHz, which is part of the ARMv6 architecture
* GPU: VideoCore IV GPU capable of hardware-accelerated 1080p H.264 video decoding and OpenGL ES 2.0 graphics
* Memory: 512MB RAM
* Storage: SD card (full-size) for booting the operating system and storing data
* Video: HDMI port and composite video (via RCA jack)
* Audio: 3.5mm analog audio jack and audio over HDMI5.
* Networking: 10/100 Ethernet RJ45 port
* GPIO: 26-pin GPIO header providing access to interfaces like UART, I2C, and SPI
* USB: 2 × USB 2.0

Powered via a 5V microUSB connector. Power consumption ranges 300mA (1.5W) - 700mA (3.5W) (depending on connected peripherals and workload)

# What OS to use?
It all comes down to the GPU support, if you don't care about GPU and performance, you can still use any 32 bit version:
https://www.raspberrypi.com/software/operating-systems/

For the experiments I will stick with the latest Buster, because it's one of the last OS that supported the GPU of this model:
http://downloads.raspberrypi.com/raspios_oldstable_armhf/images/raspios_oldstable_armhf-2023-05-03/

# What is Framebuffer?

# Raspberry PI GPU

https://www.khronos.org/opengles/

https://github.com/raspberrypi/userland


# Running DOS applications

# Using SPI with ST7789 Display

# VNC

