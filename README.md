# What is this?
A set of experiments revisiting the **Raspberry Pi Model B 512MB** in 2025 to see what it is capable. Since desktop environment is too heavy for it, my focus will be on a thing called **framebuffer**, in headless/console mode, which still allow us to use the GPU and graphics.

# Raspberry Pi Model B rev 2 512MB
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/745bd5f6-3090-40f0-906b-0cd78af5f0d5" />

* SoC: Broadcom BCM2835
* CPU: Single-core ARM1176JZF-S 32-bit CPU running at 700 MHz, which is part of the ARMv6 architecture
* GPU: VideoCore IV GPU capable of hardware-accelerated 1080p H.264 video decoding and OpenGL ES 2.0 graphics
* Memory: 512MB RAM
* Storage: SD card (full-size) for booting the operating system and storing data
* Video: HDMI port and composite video (via RCA jack)
* Audio: 3.5mm analog audio jack and audio over HDMI
* Networking: 10/100 Ethernet RJ45 port
* GPIO: 26-pin GPIO header providing access to interfaces like UART, I2C, and SPI
* CSI (camera) port: standard 15-pin, 1.0mm pitch, 16mm width   
* DSI (display) port: standard 15-pin, 1.0mm pitch, 16mm width  
* USB: 2 × USB 2.0

Powered via a 5V microUSB connector. Power consumption ranges 300mA (1.5W) - 700mA (3.5W) (depending on connected peripherals and workload)

# What OS to use?
It all comes down to the GPU support, if you don't care about GPU and performance, you can use any 32 bit version:
https://www.raspberrypi.com/software/operating-systems/

For the experiments I will stick with the latest Raspbian Buster, because it's one of the last OS that supported the GPU of this model:  
http://downloads.raspberrypi.com/raspios_oldstable_armhf/images/raspios_oldstable_armhf-2023-05-03/

# Raspberry Pi GPU
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

# What is framebuffer?
The Linux framebuffer is a simple interface that provides direct access to the display. It is represented by the device file /dev/fb0.  
Its core function is to act as a memory buffer (a RAM-based array of pixels) that holds the exact image being sent to the screen. The graphics hardware reads from this buffer continuously to refresh the display.  

Here’s how it relates to key concepts:  
* `/dev/fb0`: This is the device file. Writing pixel data to this file changes the image on the screen immediately.  
* `Console Only / Headless`: On systems without a graphical desktop (like a text-only terminal or a headless device with a display attached), the framebuffer is what renders the text console and allows basic graphics to be drawn.  
* `Raspberry Pi & Embedded Systems`: It is essential here because it is lightweight and doesn't require a complex graphical environment. It is the standard way to output graphics or video efficiently on low-power devices, making it ideal for kiosks, embedded displays, or media players.  
In essence, the framebuffer is the most basic way to control a display in Linux, making it critical for embedded and minimalist systems where performance and simplicity are paramount.  

# Performance tips
* Lower Screen Resolution: Set a lower framebuffer resolution in `config.txt`. This reduces the workload on the GPU and frees up RAM  
* Overclocking: Safely overclock the CPU from 700MHz to 1GHz using the `config.txt` file. This provides a direct CPU speed boost  
* Heatsink/Cooling: Essential if overclocking. A simple heatsink helps dissipate heat and prevents the system from throttling due to high temperatures  
* Light drivers: Use things that have built-in drivers in kernel since they tend to be smaller, I have an USB Wifi device which driver eats a lot of RAM compared to another older USB wifi card model that has built-in driver support  
* Fast SD Card: Use a Class 10 or UHS-I microSD card with high read/write speeds (beware of fake ones). The entire system runs from the SD card, so a slow card bottlenecks everything  
* Reduce GPU RAM: In `config.txt`, lower gpu_mem (e.g., gpu_mem=16). This allocates more of the limited 512MB of total RAM to the system instead of the GPU, which is crucial for memory-heavy tasks unless you need high-resolution graphics  
* Disable Unused Services: Stop background services you don't need (e.g., bluetooth, avahi-daemon). Use a tool like `raspi-config` or `systemctl disable` to free up CPU and RAM  
* Use ZRAM (Swap Compression): Create a compressed swap area in RAM. This is much faster than using a slow SD card for traditional swap and helps prevent out-of-memory crashes  
* Power Supply: Use a high-quality 5V/1.5A power adapter. Under-voltage causes throttling, slowdowns, and instability  
* Optimize Software: Use efficient, compiled languages like C over interpreted ones like Python for CPU-intensive tasks

# My setup
I will use the following settings in `/boot/config.txt`:
```
framebuffer_width=640
framebuffer_height=480
framebuffer_depth=16
arm_freq=800
gpu_mem=128
```
These will impact the experiments/syntax you will see ahead, so make sure to adjust as needed for your case

# Experiments

## Images
Linux framebuffer imageviewer supports PhotoCD, jpeg, ppm, gif, tiff, xpm, xwd, bmp, png and webp, ex:    
```bash
$ fbi image.jpeg
```

For PDF use:
```bash
$ fbgs file.pdf
```

Watch both tools in action:  
[![Watch the video](https://img.youtube.com/vi/yyO0KxJruYs/0.jpg)](https://www.youtube.com/watch?v=yyO0KxJruYs)
[![Watch the video](https://img.youtube.com/vi/isw05usOtlc/0.jpg)](https://www.youtube.com/watch?v=isw05usOtlc)  

## Videos
Using software renderer, which means CPU and **very slow**:
```bash
mplayer video.mp4 -vo fbdev2 -vf scale=640:480
```

Using hardware/GPU h264 decoder acceleration:
```bash
omxplayer video.mp4 
```

> [!WARNING]
> Omxplayer is no longer available to install from latest OS repos and has been deprecated since 2020
> https://github.com/popcornmix/omxplayer  
> But still available from the last Raspbian Buster OS with `sudo apt install omxplayer`
> They claim VLC replaces it, but I couldn't use VLC on terminal, much less with the rpi GPU

Watch the difference between both playing the same h264 encoded 480p video, mplayer(CPU) Vs omxplayer(GPU):  
[![Watch the video](https://img.youtube.com/vi/3zUy5Sg55xg/0.jpg)](https://www.youtube.com/watch?v=3zUy5Sg55xg)  

## Userland GPU demos
Userland was mentioned in the GPU section, let's run the demos and finally see the GPU in action:
```bash
$ git clone https://github.com/raspberrypi/userland
$ cd userland
$ ./buildme
$ cd host_applications/linux/apps/hello_pi/
$ ./rebuild.sh
```
In the `hello_pi` folder you will find demos, run any `.bin` file from any of them, ex:
```bash
$ cd hello_triangle
$ ./hello_triangle.bin
```
Watch them in action:  
[![Watch the video](https://img.youtube.com/vi/jPbkeuRi7ZE/0.jpg)](https://www.youtube.com/watch?v=jPbkeuRi7ZE)  

# DOOM
To run DOOM you can use this framebuffer ready version:  
```bash
$ git clone https://github.com/maximevince/fbDOOM
$ cd fbDOOM
$ wget https://github.com/Akbar30Bill/DOOM_wads/raw/refs/heads/master/doom1.wad
$ make NOSDL=1
$ ./fbdoom -iwad doom1.wad
```
See it in action:  
[![Watch the video](https://img.youtube.com/vi/T6gzGYftMPc/0.jpg)](https://www.youtube.com/watch?v=T6gzGYftMPc)  

> [!TIP]
> I am providing the static compiled binary too, so you don't need to compile, just download and run it!

## VNC
You can use VNC with console only! a desktop environment is not needed. So "Why not just use a simple SSH connection?" the key difference is:  
* SSH connection provides a text-only terminal. It's perfect for command-line control but cannot display any graphics or applications that render to the screen  
* VNC server like x11vnc mirrors the entire visual display. This means you can remotely see everything that would appear on the physical monitor  

Start the server on the raspberry:
```bash
sudo x11vnc -rawfb console@640x480x16 -auth /dev/null -noxdamage -forever -shared  -repeat -defer 0 -wait 0  -noxinerama -nowf  -nowcr -speeds modem -tightfilexfer
```

Now on any another device run the client, ex:
```bash
vncviewer -SecurityTypes None <Raspberry IP address>:0 -CompressLevel 0 -QualityLevel 0 -FullColor 0 -PreferredEncoding raw -AutoSelect=0
```

I tested a couple VNC clients, some of them will allow more options than the others, aiming reducing lag, TigerVNC was the winner so far, so try a couple yourself. Here's a video:  
[![Watch the video](https://img.youtube.com/vi/rV3fqgPKwvI/0.jpg)](https://www.youtube.com/watch?v=rV3fqgPKwvI)  

Notice how they are all mirroring each other, any input from one, will reflect on all the others  

> [!IMPORTANT]
> Doom is to showcase the lag on each device and graphics capabilities, keep in mind that is all being transmitted via Wifi, results may vary depending on your connection quality and speed. If you want less lag, use an ethernet cable connection instead  

# USB camera

# Running DOS

# Using SPI with ST7789 display
