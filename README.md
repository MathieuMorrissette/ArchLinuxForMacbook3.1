# ArchLinuxForMacbook3.1
Tutorial


## Fixing backlight

Run this command to get info about the device

```
sudo Xorg :1 -configure
```

Create this file in /etc/X11/xorg.conf.d/nvidia.conf 
Change infos according the the xorg.conf.new file created in
root by the previous command

```
Section "Device"
    VendorName     "NVIDIA Corporation"
    BoardName      "GeForce 320M"
    Option         "RegistryDwords" "EnableBrightnessControl=1"
    Identifier  "Card0"
    Driver      "nvidia"
    BusID       "PCI:2:0:0"
EndSection
```

Source : https://help.ubuntu.com/community/MacBookAir3-2/Raring

# Nvidia driver

Nouveau driver is broken for this mac and so we must install the proprietary driver

Install nvidia-340xx package

Do not reboot yet or you will get a black screen 

On this mac PCI-E lanes must be enabled manually at boot
So we need to create a grub script -> https://askubuntu.com/questions/264247/proprietary-nvidia-drivers-with-efi-on-mac-to-prevent-overheating/613573#613573

Run the following command :

```
sudo lshw -businfo -class bridge -class display
```

The output of the lshw should look like this :

```
Bus info          Device    Class          Description
======================================================
pci@0000:00:00.0            bridge         MCP89 HOST Bridge
pci@0000:00:03.0            bridge         MCP89 LPC Bridge
pci@0000:00:15.0            bridge         NVIDIA Corporation
pci@0000:00:17.0            bridge         MCP89 PCI Express Bridge
pci@0000:02:00.0            display        MCP89 [GeForce 320M]

```

Take note of the last 2 lines


Create this file 

```
sudo nano /etc/grub.d/01_enable_vga.conf
```

then run this command : ( change the info according to your previous command output )

```
cat << EOF
setpci -s "00:17.0" 3e.b=8
setpci -s "02:00.0" 04.b=7
EOF
```

Then run to make it executable

```
sudo chmod 755 /etc/grub.d/01_enable_vga.conf
```

Then update grub

```
grub-mkconfig -o /boot/grub/grub.cfg`
```

# Fix tty

If you try to go in tty you will get this error

```
[   71.819875] caller _nv000788rm+0xe4/0x1c0 [nvidia] mapping multiple BARs
[   72.226863] NVRM: Your system is not currently configured to drive a VGA console
[   72.226867] NVRM: on the primary VGA device. The NVIDIA Linux graphics driver
[   72.226869] NVRM: requires the use of a text-mode VGA console. Use of other console
[   72.226871] NVRM: drivers including, but not limited to, vesafb, may result in
[   72.226872] NVRM: corruption and stability problems, and is not supported.
[   72.238728] ------------[ cut here ]------------
```

install gtk2 ( for nvidia settings server )

install acpid to fix issues with xorg

```
systemctl enable acpid
systemctl start acpid
```


Keywords :
Macbook air 3.1 3,1 2010 mid late 320m nvidia
