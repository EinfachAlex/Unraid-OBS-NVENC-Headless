# Unraid-OBS-NVENC-Headless
Run OBS on a headless unraid server with gpu acceleration (NVENC)

# How-To
## 1. Create a VM
- Create a Ubuntu 20.04 VM in Unraid
- 15 GB Disk
- Graphics Card 1: VNC
- Graphics Card 2: Your GPU
- Sound Card: Your GPU

- Install Ubuntu on the VM
## 2. Install TurboVNC and VirtualGL
- Boot the VM
- Press STRG + Alt + F3 to switch into terminal mode
- `sudo apt install openssh-server`
- Connect to your VM via SSH with [MobaXTerm](https://mobaxterm.mobatek.net/ "MobaXTerm") on your host
- Download [VirtualGL](https://sourceforge.net/projects/virtualgl/files/3.0.1/ "VirtualGL") and [TurboVNC](https://sourceforge.net/projects/turbovnc/files/3.0/ "TurboVNC")
- `sudo dpkg -i <name of your downloaded files>`
- `sudo apt -fix-broken install`
- `sudo dpkg -i <name of your downloaded files>` (again)

## 2.1 Test if VirtualGL works
- `sudo apt install mesa-utils`
- `glxgears`
    - If you're connected to the server with MobaXTerm a new window should open on your PC displaying the glxgears application

## 3. Install NVIDIA Drivers
- Download NVIDIA Driver for your GPU from https://www.nvidia.de/Download/index.aspx?lang=de

- Disable Nouveau-Driver
  `sudo nano /etc/modprobe.d/blacklist-nouveau.conf`
  Paste the following into the file `blacklist nouveau
  options nouveau modeset=0`
    - `sudo update-initramfs -u`
      `sudo reboot`
    - Connect to SSH again, after your VM rebooted

`sudo apt install build-essential`

- Execute the .run file you downloaded from the NVIDIA-Website earlier
    - Continue Installation
    - Yes
    - Ok
    - Yes
    - Ok

## 4. Edit XConfig
- Get your PCI BusID from `nvidia-xconfig --query-gpu-info`
- `sudo nvidia-xconfig -a --allow-empty-initial-configuration --use-display-device=None --virtual=1920x1080 --busid <THE PCI BusID>`
- `sudo nano /etc/X11/xorg.conf`
    - Add `Option "HardDPMS" "false"` under `Section "Device"`

## 5. Configure VirtualGL
- `/opt/VirtualGL/bin/vglserver_config`
- 1
- Y
- n
- n
- Y
- X

## 6. Finally start the VNC Server
- `/opt/TurboVNC/bin/vncserver`
- `/opt/TurboVNC/bin/vncviewer`
    - A new "TurboVNC" window should appear on your PC
        - Enter "localhost"
        - Click connect
        - Click Yes and enter your SSH password
        - Click connect on localhost:1
        - A new window should open where you are connected to your Ubuntu VM Desktop

## 7. Install and use OBS
Use the following commands on the TurboVNC Ubuntu Desktop
- `export DISPLAY=:1`
- `sudo add-apt-repository ppa:obsproject/obs-studio && sudo apt update && sudo apt install ffmpeg obs-studio`
- `vglrun obs` to start obs using GPU.
- You can now use OBS and change settings as you like.
- You can also install [OBS-NDI](https://github.com/Palakis/obs-ndi " OBS-NDI")
