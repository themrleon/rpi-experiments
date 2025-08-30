# What is this?
Set of experiments revisiting the **Raspberry Pi Model B 512MB** in 2025 to see what it is capable. Since desktop environment is too heavy for it, my focus will be on a thing called **framebuffer**, in headless/console mode, which still allow us to use the GPU and graphics!

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

For the experiments I will stick with the latest Debian Buster, because it's one of the last OS that supported the GPU of this model:
http://downloads.raspberrypi.com/raspios_oldstable_armhf/images/raspios_oldstable_armhf-2023-05-03/

# Raspberry PI GPU
I couldn't put in better words than AI, so here it goes:

🖥️ GPU Basics  
VideoCore IV GPU: This handles graphics and video playback. It supports hardware-accelerated 1080p video decoding (like H.264) and basic 3D graphics.
Proprietary Components: The GPU requires closed-source firmware (a "binary blob") to function. This firmware is loaded during boot-up and is essential for the GPU to operate, as it contains the low-level code that controls the hardware.
While some parts of the driver stack are open-source, the core GPU firmware and certain userland components remain proprietary. This is common in many embedded systems to protect intellectual property.

🎮 OpenGL ES Support  
The GPU supports OpenGL ES 2.0 (and possibly limited ES 3.0 features in some contexts, though primarily ES 2.0 is the standard for this model).
OpenGL ES (OpenGL for Embedded Systems) is a streamlined version of OpenGL designed for devices like the Pi. It allows developers to create 3D graphics applications.
On the Pi 1, OpenGL ES performance is functional but limited due to the hardware's age and single-core CPU.

🔧 Drivers and Mesa  
Drivers: The Raspberry Pi uses a mix of open and closed drivers. The closed-source components include the GPU firmware and some userland libraries.
Mesa: This is an open-source implementation of OpenGL and other graphics APIs. On the Pi 1, Mesa can be used to provide OpenGL support, but it may rely on software rendering (which is slower) or interface with the proprietary GPU firmware for hardware acceleration.
For the Pi 1, using Mesa with hardware acceleration typically requires enabling experimental drivers and adjusting memory settings (like cma-128 in config.txt), but performance may still be slower than on newer Pi models.

🖼️ Dispmanx  
Dispmanx is a low-level display manager API specific to Raspberry Pi. It handles graphics layers and composition directly, often used for applications that need precise control over the display (e.g., video players or embedded systems).
It allows elements like video overlays or GUI elements to be layered efficiently. However, it is a proprietary API and not widely used outside the Pi ecosystem.
Dispmanx can be complex to use directly, as it involves managing resources and memory carefully to avoid performance issues (e.g., slow buffer writes).

🖼️ OpenMAX  
OpenMAX is a standardized, but proprietary, API that provides direct access to the GPU's hardware acceleration for demanding multimedia tasks like decoding 1080p video. It is implemented as a closed-source "binary blob" provided by Broadcom, meaning developers can only use the pre-built components (like video decoders) and cannot create their own. While powerful, programming with OpenMAX directly is complex and low-level, so it's often easier to use it indirectly through simpler frameworks like GStreamer, which handle the complicated details while still benefiting from the GPU's speed.

⚖️ Open vs. Closed Source  
The GPU's proprietary nature means that some advanced features or bug fixes rely on Broadcom or Raspberry Pi-provided updates.
While this closed approach has drawbacks (e.g., less community control), it also ensured the Pi could launch at a low cost with capable multimedia features.

💎 Performance Notes  
The Pi 1's GPU is capable for its time (released in 2012) but struggles with modern graphics workloads. Simple 3D applications and video playback work, but complex tasks may be slow.
Techniques like overclocking or adjusting gpu_mem in config.txt can help allocate more RAM to the GPU, potentially improving graphics performance.
In short, the Raspberry Pi 1 Model B's GPU is a capable but aging piece of hardware that relies on a mix of open and closed software components to deliver its graphics functionality. While it supports standards like OpenGL ES 2.0, its performance is limited by its hardware design and proprietary drivers.

# What is Framebuffer?

# Userland
https://github.com/raspberrypi/userland

# Running DOS applications

# Using SPI with ST7789 Display

# VNC

