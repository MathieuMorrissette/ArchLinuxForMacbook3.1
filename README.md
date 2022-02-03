# ArchLinuxForMacbook3.1 & 7.1 (Pro)
Tutorial

## Caution

Linux Kernel 5.1 broke the sound driver it sounds all scratchy and bad.
SDDM with nouveau doesn't work  on kernel 5.1 with this hardware.

https://bbs.archlinux.org/viewtopic.php?id=246826


# EFI

### **Fixing backlight**

1. Run this command to get info about the device

    ```
    sudo Xorg :1 -configure
    ```

2. Create this file in */etc/X11/xorg.conf.d/nvidia.conf* and change the informations according to the the xorg.conf.new file created by the previous command.

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

### **Nvidia driver**

Nouveau driver is broken for this mac and so we must install the proprietary driver

1. Install nvidia-340xx package and do not reboot. If you reboot at this step you will get a black screen.

    > update : the nvidia-340xx package is no longer maintained please install it from the AUR.
    https://aur.archlinux.org/nvidia-340xx.git




    > On these macs PCI-E lanes must be enabled manually at boot and
    so we need to create a grub script 
    https://askubuntu.com/questions/264247/proprietary-nvidia-drivers-with-efi-on-mac-to-prevent-overheating/613573#613573

2. Run the following command and take note of the last 2 lines

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

3. Create this file 

    ```
    sudo nano /etc/grub.d/01_enable_vga.conf
    ```

4. Add the following in it : 


    ```
    cat << EOF
    setpci -s "00:17.0" 3e.b=8
    setpci -s "02:00.0" 04.b=7
    EOF
    ```
    > Change the info according to your previous command output

5. Make it readable and executable.

    ```
    sudo chmod 755 /etc/grub.d/01_enable_vga.conf
    ```

6. Update grub

    ```
    grub-mkconfig -o /boot/grub/grub.cfg`
    ```


7. Append the following to the */etc/X11/xorg.conf.d/nvidia.conf*  file 

    ```
    Section "ServerFlags" Option "IgnoreABI" "1" EndSection
    ```



### **Fix tty**

Run the arch in legacy mode or csm
https://wiki.archlinux.org/index.php/MacBookPro7,1

I made an hybrid MBR and booted it with refind

# CSM/ LEGACY

works out of the box


# GENERAL

# Fix long sddm load times
It is because of low entropy

Install rng-tools package
enable the service

```
systemctl enable rngd.service
```

Keywords :
Macbook air 3.1 3,1 2010 mid late 320m nvidia
