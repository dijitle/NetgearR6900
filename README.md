# NetgearR6900
Flash your Netgear R6900 router with custom firmware

---
## Situation
I bought a Netgear R6900 from Costco a while back and served well as a Wi-Fi router in my 1-bedroom condo. Once I moved to my house, my ISP provided fiber right into my house but only offered a modem/router combo which the service would not work without. Thankfully the wireless on it was decent, dual band AC, with a decent user interface for controlling settings and whatnot. My home was wired with cat5e to the master, living and WAP upstairs with the central hub in the back corner of the basement. Since the modem had to be connected to the fiber-to-Ethernet converter, I couldn't have the modem/router upstairs, otherwise, I would lose the wired hub in the basement. So, the router sits in the back corner of the basement. I used 1 Ethernet port for my living room with an Ethernet switch to connect my TV, Xbox, apple tv, etc. hard-wired. Great! I used another Ethernet port to connect the WAP upstairs extending the Wi-Fi network upstairs to improve signal for wireless devices. Big problem was the other bedroom in the back of the house did not have wired Ethernet and my computer didn't have a Wi-Fi card so I thought, since I have this pretty decent R6900, why not use it as a bridge? Using Netgear's interface was lackluster to say the least, and after I managed to get it set up, after a day or so, the connection would randomly die and I would have to reset my router and start all over again. I knew about customer firmware like DD-WRT and Tomato and wondered if I could install it on here and have more control. Sadly, they only had custom firmware for the R7000, the non-Costco version. trying to install any custom firmware on the R6900 proved futile. Reading up on this, the differences between the R6900 vs R7000 seemed to be artificial imposed rather than technically limited. Not sure why they would do this but here we are… I found a few resources I will link below, but the information was scattered and things like “look up how to do it, there’s ton of info” is hardly a good enough answer when you just want a step-by-step guide to getting something done. So, I thought I’d dedicate some time to write this guide and save someone else some time and swear-words and potentially money. You’re welcome!

## Disclaimer
Do at your own risk. I bare absoultely zero responsiblity for anything that goes wrong. You've been warned!

## Step-by-Step putting Tomato Router onto your R6900

1) Reset your R6900 back to factory defaults by pressing the `Reset` pin button with a paperclip on the back left of Ethranet Port 4. Hold for ~10 seconds.
2) Disconnect everything except your computer (I'm using Windows 10) directly to Ethranet port 1. No Internet.
3) Revert back to the [inital firmware version 1.0.0.2](https://www.netgear.com/support/product/R6900.aspx#download) I've also included it directly in this repo in case netgear decides to take it down. This is what blocked me for a while as the follow steps will NOT work without this. Later firmwares purposefully blocked it. 
    1) Go to http://192.168.1.1
    2) Default user/password should be `admin` / `password` 
    3) It should complain there's not internet, just say you'll set it up later. 
    4) It may prompt you to set up a new password, do it, if not, set it to a new password in `Advanced` > `Administration` > `Set Password`
    5) Grab your MAC address for the router from the Router Information widget. You'll need to enter it later
    5) Go to `Advanced` tab, `Administration` > `Firmware Upgrade` and upload 1.0.0.2 and click `Upload`
4) Enable Telnet
    1) There are several ways detailed [here](https://oldwiki.archive.openwrt.org/toh/netgear/telnet.console)
    2) Linux seems to be the preferred way as most of the tools are linux-centered but windows ports do exist. I decided to use Docker for windows.   

In powershell:
```
> docker pull centos        
```
```
> docker run -it centos /bin/bash
```

Inside docker container
```
yum install gcc -y
```
```
yum install git -y
```
```
yum install telnet -y
```
```
git clone https://github.com/insanid/NetgearTelnetEnable.git
```
```
cd NetgearTelnetEnable/
```
```
gcc -o telnetenable md5.c blowfish.c telnetenable.c
```
```
./telnetenable 192.168.1.1 ABCDEF01234 admin yourpassword
```
```
telnet 192.168.1.1
```

Inside telnet
```
burnboardid U12H270T00_NETGEAR
```
```
nvram erase && reboot
```
5) Your router should restart. Go to `Advanced` tab, `Administration` > `Firmware Upgrade` and upload your favorite custom firware for the R7000

## Links
* https://www.myopenrouter.com/forum/netgear-r6900-dd-wrt-firmware-mod
* https://forum.dd-wrt.com/phpBB2/viewtopic.php?t=301328&sid=ccf1fc8b96fb0d0e09a753319fad8420
* https://oldwiki.archive.openwrt.org/toh/netgear/telnet.console
* https://community.netgear.com/t5/Nighthawk-WiFi-Routers/R7000-vs-R6900-CostCo-Differences-and-availability-of-3rd-party/td-p/1051243