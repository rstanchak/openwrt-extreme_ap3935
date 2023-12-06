
checkout OpenWRT
```
# clone openwrt.  The master branch is the default
git clone https://git.openwrt.org/openwrt/openwrt.git

# update and install feeds
cd openwrt/
./scripts/feeds update -a
./scripts/feeds install -a

# launch menu-based configuration
make menuconfig


```
  make -j$(nproc) defconfig download clean world

```


```
Hit 'd' for diagnostics, or any other key to stop autoboot:  0
```
Press any key to stop autoboot. It may take a couple seconds, but then 

```
...
login: admin
password: new2day
```
You should see a u-boot prompt.  Enter the following commands
```

Boot (BAK)->

Boot (BAK)-> setenv ipaddr 10.0.0.2
Boot (BAK)-> ping 10.0.0.1
Full duplex link
Link 0 up, Phy_status = bc5c
Port:0 speed 1000Mbps
Using eth0 device
host 10.0.0.1 is alive
Boot (BAK)-> 

```

https://forum.openwrt.org/t/adding-openwrt-support-for-extreme-network-ap3935i/87234/58

https://github.com/dmascord/openwrt/commit/356e98db7cac873dcd1a9c89d6c9360c436f094e

Start TFTP
```
sudo ./venv/bin/tftpy_server.py -r openwrt/bin/targets/ipq806x/generic/
```

On the serial terminal, enter the u-boot environment and enter the following
```
set MOSTRECENTKERNEL 0
set serverip 10.0.0.1
set ipaddr 10.0.0.2
set bootcmd 'ping 10.0.0.1; run boot_flash'
saveenv
tftpboot openwrt-ipq806x-generic-extreme_ap3935-initramfs-uImage
bootm
```

Once the kernel and initramfs loads, hit the enter key to get openwrt shell.  It takes a little time

Modify

/etc/config/network

config interface 'lan'
        option device 'br-lan'
        option proto 'static'
        option ipaddr '10.0.0.2'
        option netmask '255.255.255.0'
        option ip6assign '60'

/etc/init.d/network restart

scp roman@10.0.0.1:rstanchak/project-extreme-ap/openwrt/bin/targets/ipq806x/generic/openwrt-ipq806x-generic-extreme_ap3935-squashfs-nand-sysupgrade.bin /tmp

sysupgrade /tmp/openwrt-ipq806x-generic-extreme_ap3935-squashfs-nand-sysupgrade.bin
