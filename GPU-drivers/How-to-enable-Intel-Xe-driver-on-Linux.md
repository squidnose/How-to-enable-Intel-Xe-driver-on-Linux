# THE Xe DRIVERS ARE EXPERIMENTAL AND MAY BREAK SOMETHING!!!
I did not have any issues on Voidlinux with kernel 6.12 but other distros may vary. 
# How-to-enable-Intel-Xe-driver-on-Linux
- A guide on how to enable the experimental Xe driver for Intel on linux.
- I have tested this on a HP laptop with the Core i7 1165G7.
- Youtube Video: https://youtu.be/ZPh_oBGWoHc?si=cwU9YO0frrI5EWvH
## 1.Requirements
- A terminal
- The lspci command
- A text editor of choice (kate, nano, mousepad, vim, etc)
- Linux 6.8+ for the intel Xe driver. (Newer kernels will have better performance and or bug fixes)

## 2.Find out what driver you are using
```
lspci -k -d ::03xx
```
- k | show kernel driver information
- d [vendor]:[device_class] | Only list devices whose PCI class is 03 (Display) and any subclass
  - 03 = Display controller class (according to PCI class codes)
  - xx = “match any subclass” (wildcard for subclasses such as: 00 VGA controller, 80 3D controller, etc)

### Example output
```
0000:00:02.0 VGA compatible controller: Intel Corporation TigerLake-LP GT2 [Iris Xe Graphics] (rev 01)
        Subsystem: Hewlett-Packard Company Device 8820
        Kernel driver in use: i915
        Kernel modules: i915, xe
```
- i915 is in use, i915 and xe are avaliable.

## 3.Find out what PCI ID your gpu has
```
lspci -nn | grep Xe
```
- nn | Tells lspci to show both the human-readable name and the numeric PCI IDs.
- Grep Xe shows only gpus with Xe in the name. Use without "| grep Xe" if you dont see anything.

### Example Output
```
0000:00:02.0 VGA compatible controller [0300]: Intel Corporation TigerLake-LP GT2 [Iris Xe Graphics] [8086:9a49] (rev 01)
```
- 9a49 is the PCIe ID
## 4.Add kernel boot parameter
- Most distros use grub as there bootloader.
- You will need to add theese kernel boot parameters to grub:
```
i915.force_probe=!<PCI ID>
xe.force_probe=<PCI ID>
```

### Open Grub config
- i will use kate, but you can also use sudo nano:
```
kate /etc/default/grub
```
- Here you will change this line:
```
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=4"
```
and change it to:
```
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=4 i915.force_probe=!9a49 xe.force_probe=9a49"
```
- Replace 9a49 with your PCI ID. 
- "loglevel=4" is the defualt kernel parameter on Voidlinux.
- "loglevel=4" symbolizes any previous kernel parameters. If you did not have any, there is no need to add loglevel=4.
### Update grub
```
sudo update-grub
```
Check for any erros in the output. 
## 5.Reboot to apply changes
After a reboot, check what driver you are using again:
```
lspci -k -d ::03xx
```
It should look something like:
```
0000:00:02.0 VGA compatible controller: Intel Corporation TigerLake-LP GT2 [Iris Xe Graphics] (rev 01)
        Subsystem: Hewlett-Packard Company Device 8820
        Kernel driver in use: xe
        Kernel modules: i915, xe
```
## Links to more Info
- https://www.faceofit.com/intel-arc-iris-xe-linux-driver-compatibility-guide/
- https://wiki.archlinux.org/title/Intel_graphics
