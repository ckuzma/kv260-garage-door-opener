# kv260-garage-door-opener

## Background

This repository currently serves merely as the home for everything I've done with the Xilinx Kria KV260
developer kit.  My original intent was to utilize the Kria SOM, extension board, and camera to automatically
raise (or lower) the garage door to my house by employing machine computer vision to recognize my car as
I approach or leave.  Given that a close equivalent is Tesla's $325 "Automatic Garage Door Opener", this
could thus be thought of as a car-brand-agnostic poor-man's version of that system.

Unfortunately, as a result of my current housing situation, I ran into a number of issues that have so
far prevented me from making any meaningful progress on my project.  Owing to the fact that I am at present
hopping between Airbnbs while most of my gear sits in storage in another city, I have lacked access to basic
input peripherals, networking gear, LCD monitor, and most importantly a stable place where the KV260 could
be put to work learning when to trigger the garage door to open.

So instead of actually coding software and interfacing hardware, I have spent all of my time during the
[Hackster "2021 Adaptive Computing Challenge"](https://www.hackster.io/contests/xilinxadaptivecomputing2021)
attempting to bypass the limitations of my current hacking situation, much to no avail.  Be it hours spent
attempting to compile Realtek and Mediatek USB WiFi dongle drivers, repurposing a Creality WiFi box to serve
as a WiFi-to-Ethernet bridge, or attempting to obtain serve up a window manager via VNC, nothing has worked.

All of which means that I have currently thrown in the towel on completing this project within the scope of
the hackathon.  That being said I do still intend to use the hardware for its intended purpose as I firmly
believe in the usefulness of this project.  Garage door openers and their associated remote controls have
changed very little since their invention-- at least in terms of how a user interacts with them-- and I feel
that this is ripe for change.

To AMD/Xilinx and the Hackster crew... I apologize for any disappointment I may have invoked.  But know that
I do intend to complete my project and will document and share it with the rest of the world in the future!

-----

### Raw Project Log Notes

- Flashed firmware to card, booted up connected to ethernet (headless)

- SSH'd into KV260 `ssh ubuntu@10.0.0.214`
    - default password was `ubuntu`, changed it to `hackster`

- Updated the system `sudo apt update` and `sudo apt upgrade`

- Documentation for WiFi: https://www.hackster.io/dramoz/adding-usb-wifi-for-the-kria-kv260-ubuntu-20-04-3-b5e8ea


### WiFi Driver

- `lsusb -v` output
    ```
    Bus 001 Device 003: ID 0bda:0811 Realtek Semiconductor Corp. 
    Couldn't open device, some information will be missing
    Device Descriptor:
    bLength                18
    bDescriptorType         1
    bcdUSB               2.10
    bDeviceClass            0 
    bDeviceSubClass         0 
    bDeviceProtocol         0 
    bMaxPacketSize0        64
    idVendor           0x0bda Realtek Semiconductor Corp.
    idProduct          0x0811 
    bcdDevice            2.00
    iManufacturer           1 
    iProduct                2 
    iSerial                 3 
    bNumConfigurations      1
    Configuration Descriptor:
        bLength                 9
        bDescriptorType         2
        wTotalLength       0x003c
        bNumInterfaces          1
        bConfigurationValue     1
        iConfiguration          0 
        bmAttributes         0xe0
        Self Powered
        Remote Wakeup
        MaxPower              500mA
        Interface Descriptor:
        bLength                 9
        bDescriptorType         4
        bInterfaceNumber        0
        bAlternateSetting       0
        bNumEndpoints           6
        bInterfaceClass       255 Vendor Specific Class
        bInterfaceSubClass    255 Vendor Specific Subclass
        bInterfaceProtocol    255 Vendor Specific Protocol
        iInterface              2 
        Endpoint Descriptor:
            bLength                 7
            bDescriptorType         5
            bEndpointAddress     0x84  EP 4 IN
            bmAttributes            2
            Transfer Type            Bulk
            Synch Type               None
            Usage Type               Data
            wMaxPacketSize     0x0200  1x 512 bytes
            bInterval               0
        Endpoint Descriptor:
            bLength                 7
            bDescriptorType         5
            bEndpointAddress     0x05  EP 5 OUT
            bmAttributes            2
            Transfer Type            Bulk
            Synch Type               None
            Usage Type               Data
            wMaxPacketSize     0x0200  1x 512 bytes
            bInterval               0
        Endpoint Descriptor:
            bLength                 7
            bDescriptorType         5
            bEndpointAddress     0x06  EP 6 OUT
            bmAttributes            2
            Transfer Type            Bulk
            Synch Type               None
            Usage Type               Data
            wMaxPacketSize     0x0200  1x 512 bytes
            bInterval               0
        Endpoint Descriptor:
            bLength                 7
            bDescriptorType         5
            bEndpointAddress     0x87  EP 7 IN
            bmAttributes            3
            Transfer Type            Interrupt
            Synch Type               None
            Usage Type               Data
            wMaxPacketSize     0x0040  1x 64 bytes
            bInterval               3
        Endpoint Descriptor:
            bLength                 7
            bDescriptorType         5
            bEndpointAddress     0x08  EP 8 OUT
            bmAttributes            2
            Transfer Type            Bulk
            Synch Type               None
            Usage Type               Data
            wMaxPacketSize     0x0200  1x 512 bytes
            bInterval               0
        Endpoint Descriptor:
            bLength                 7
            bDescriptorType         5
            bEndpointAddress     0x09  EP 9 OUT
            bmAttributes            2
            Transfer Type            Bulk
            Synch Type               None
            Usage Type               Data
            wMaxPacketSize     0x0200  1x 512 bytes
            bInterval               0
    ```

- Guide https://ubuntuhandbook.org/index.php/2021/01/install-rtl8188eu-rtl8723ds-drivers-ppa-ubuntu-20-04/
- `sudo add-apt-repository ppa:kelebek333/kablosuz`
- `sudo apt install rtl8188eu-dkms`
        ```
    ubuntu@kria:~/git/rtl8188eus$ sudo apt install rtl8188eu-dkms
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    rtl8188eu-dkms is already the newest version (5.2.2.4~focal4).
    The following packages were automatically installed and are no longer required:
    libfwupdplugin1 libplymouth5
    Use 'sudo apt autoremove' to remove them.
    0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
    1 not fully installed or removed.
    After this operation, 0 B of additional disk space will be used.
    Do you want to continue? [Y/n] Y
    Setting up rtl8188eu-dkms (5.2.2.4~focal4) ...
    Removing old rtl8188eu-5.2.2.4~ppa DKMS files...

    -------- Uninstall Beginning --------
    Module:  rtl8188eu
    Version: 5.2.2.4~ppa
    Kernel:  5.4.0-1017-xilinx-zynqmp (aarch64)
    -------------------------------------

    Status: Before uninstall, this module version was ACTIVE on this kernel.

    8188eu.ko:
    - Uninstallation
    - Deleting from: /lib/modules/5.4.0-1017-xilinx-zynqmp/updates/dkms/
    - Original module
    - No original module was found for this module on this kernel.
    - Use the dkms install command to reinstall any previous module version.

    depmod....

    DKMS: uninstall completed.

    ------------------------------
    Deleting module version: 5.2.2.4~ppa
    completely from the DKMS tree.
    ------------------------------
    Done.
    Loading new rtl8188eu-5.2.2.4~ppa DKMS files...
    Building for 5.4.0-1017-xilinx-zynqmp
    Building initial module for 5.4.0-1017-xilinx-zynqmp
    Done.

    8188eu.ko:
    Running module version sanity check.
    - Original module
    - No original module exists within this kernel
    - Installation
    - Installing to /lib/modules/5.4.0-1017-xilinx-zynqmp/updates/dkms/

    depmod....

    DKMS: install completed.
    rtl8188eufw.bin firmware file copied to /lib/firmware/rtlwifi/
    ```
- `sudo insmod /lib/modules/5.4.0-1017-xilinx-zynqmp/updates/dkms/8188eu.ko`
- Followed own guide as found here
