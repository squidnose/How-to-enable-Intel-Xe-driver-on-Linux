# How-to-enable-Intel-Xe-driver-on-Linux
- A guide on how to enable the experimental Xe driver for Intel
- I have tested this on a HP laptop with the Core i7 1165G7. 
## Reqiurements
- A terminal
- The lspci command
- A text editor of choice

## Find out what driver you are using
```
lspci -k -d ::03xx
```
- k | show kernel driver information
- d [vendor]:[device_class] | Only list devices whose PCI class is 03 (Display) and any subclass.
  - 03 = Display controller class (according to PCI class codes)
  - xx = “match any subclass” (wildcard for subclasses such as: 00 VGA controller, 80 3D controller, etc)

### Example output
```
0000:00:02.0 VGA compatible controller: Intel Corporation TigerLake-LP GT2 [Iris Xe Graphics] (rev 01)
        Subsystem: Hewlett-Packard Company Device 8820
        Kernel driver in use: i915
        Kernel modules: i915, xe
```
- i915 is in use, i915 and xe are avaliable

## Find out what PCI ID your gpu has
```
lspci -nn | grep Xe
```
- nn | Tells lspci to show both the human-readable name and the numeric PCI IDs
- Grep Xe shows only gpus with Xe in the name. Use without "| grep Xe" if you dont see anything.

### Example Output
```
0000:00:02.0 VGA compatible controller [0300]: Intel Corporation TigerLake-LP GT2 [Iris Xe Graphics] [8086:9a49] (rev 01)
```
- 9a49 is the PCIe ID
## Add kernel boot parameter
- Most distros use grub as there bootloader.
- You will need to add theese kernel boot parameters to grub:
```
i915.force_probe=!9a49
xe.force_probe=9a49
```
REPLACE 9a49 WITH YOUR PCI ID!!!
### Open Grub config
i will use kate, but you can use sudo nano aswell:
```
kate /etc/default/grub
```
Here you will change this line:
```
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=4"
```
"loglevel=4 is the defualt kernel parameter on Voidlinux.

Change it to:
```
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=4 i915.force_probe=!9a49 xe.force_probe=9a49"
```
