#testbed-server

## Background
This repository is meant as documentation for my home servers, with information on hardware, software, and services I well be self-hosting, in an attempt to learn more about systems administration, networking, security, and specific software like Docker and Proxmox. This is a shared network with my partner and roommates and there are limitations with both hardware and the level of tinkering. My goal is to utilize primarily open source software and cheaper hardware I can get second hand off FaceBook Marketplace or eBay to tinker and cut back on my reliance of third party services such as cloud providers, streaming services, and other subscription services. I will also be exploring penetration testing and attempting to uncover security vulnerabilities in my home servers to learn how to defend myself and systems from such risks. Most of these ideas were sprouted by poor Wi-Fi connection to my bedroom and initially using a Raspberry Pi 3B+ as an access point.


ansible and scripts to setup servers

wireshark
openstack
kubernetes
tcpdump
scapy
iperf

## Network Diagram
This is a network diagram showing the hardware and planned services that will be hosted (subject to change). VLAN with roommates, Router, APs, 
![alt text](example-network.png)

## Router
Our current place is on Bell's Fibre network and uses a Bell Gigahub acting as our modem and router. I want to use another machine as our router running OPNSense. 
Advanced DMZ vs PPPoE for the Bell Gigahub

## Managed Switch (Netgear M4100-26G-POE)

## TrueNAS Scale
One pool with RAIDZ1
https://www.youtube.com/watch?v=3T5wBZOm4hY

## Proxmox Cluster

### Docker and Portainer in Proxmox
https://www.youtube.com/watch?v=wrlukx-QYRw&t=281s

### Proxmox setup
https://technotim.live/posts/first-11-things-proxmox/

### Home Assistant OS in Proxmox
https://www.derekseaman.com/2023/10/home-assistant-proxmox-ve-8-0-quick-start-guide-2.html

### Stacks = Docker compose in Portainer
https://www.portainer.io/blog/stacks-docker-compose-the-portainer-way

## Access Points (WS-AP3825i)

### Resources
- [Reddit: Extreme Networks WS-AP3825i setup tutorial](https://www.reddit.com/r/openwrt/comments/1e0otf7/extreme_networks_wsap3825i_setup_tutorial/)
- [OpenWrt: Extreme Networks WS-AP3825i / WS-AP3825E](https://openwrt.org/toh/extreme_networks/ws-ap3825i)
- [OpenWrt: Wi-Fi Extender/Repeater with Bridged AP over Ethernet](https://openwrt.org/docs/guide-user/network/wifi/wifiextenders/bridgedap)

### connect a serial usb to ethernet cable from host device to the access point's console port
- `sudo picocom -b 115200 /dev/ttyUSB0`

### create a tftp server on host device and download an openwrt image
- `cd $(mktemp -d)`
- `curl https://downloads.openwrt.org/releases/23.05.5/targets/mpc85xx/p1020/openwrt-23.05.5-mpc85xx-p1020-extreme-networks_ws-ap3825i-initramfs-kernel.bin -o ws-ap3825i-initramfs.bin`
- `cp ws-ap3825i-initramfs.bin initramfs.bin`
- `sudo dnsmasq -d --enable-tftp --port 0 --tftp-root $(pwd)`

### configure and load the firmware over tftp (OpenWrt >= version 23)
- `setenv ramboot_openwrt "setenv ipaddr <ipv4 client address>; setenv serverip <tftp server address>; tftpboot 0x2000000 initramfs.bin; interrupts off; bootm 0x2000000;"`
- `setenv boot_openwrt "bootm 0xEC000000;"`
- `setenv bootcmd "run boot_openwrt";`
- `saveenv`
- `run ramboot_openwrt`

### upgrade the system
- `cd /tmp`
- `wget <latest firmware OpenWrt upgrade link above>`
- `sysupgrade -n /tmp/openwrt-mpc85xx-p1020-extreme-networks_ws-ap3825i-squashfs-sysupgrade.bin`

Reboot the system

### edit the config files using the ones provided in ./openwrt/etc/config then reboot the system

### Access LuCI by connecting to the static IP address of the access point
1. go to network -> wireless, and edit the SSIDs
2. go to the wireless security tab under interface configuration
3. set encryption to WPA2-PSK and set the Key (password)

## TODO
- UPS
- Upgrade storage
- Upgrade Jellyfin server to 7th gen intel or newer for improved transcoding
- Ollama/local LLM server with GPU (Tesla P40/P4/P100/P102-100, VRAM)
