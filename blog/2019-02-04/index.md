---
date: "2019-02-04"
title: "Prometheus"
category: "General"
---

# Running Node Exporter on Synology Nas

My home network includes a Synology DS918+ NAS for media storage, streaming, and backups. After intial setup I decided to turn my existing mediacenter (an Intel NUC i3) from a few years ago into a monitoring device. I'll go into the rest of the configuration of the home setup at some point, but the first piece of the puzzle for integrating the NAS was how do I run node_exporter on it?

Turns out it wasn't hard. I simply copied the node exporter code to the NAS device. Using scp:
`scp node_exporter sean@<NAS IP>:/volume1/homes/sean`

Few things to note there. The username will be different for your NAS device, generally whatever you setup when you setup the nas. The location "volume1" is also just the generic volume name given when I set the NAS up. The "homes" section is based on the ssh settings on the NAS. If you ssh to your NAS device and go up a directory you'll notice you find yourself in /var/services/homes - it's essentially where the home directory is created for your user when you connect. 

You can control SSH settings on the Synology NAS portal. Assuming you have that all in place, the easy part is adding the config so node exporter auto starts on reboot. To do that first escalate your priveleges in the NAS ssh session to root. To do that run `sudo -i`. Once you are escalated, you will need to create the init file: `vim /etc/init/node_exporter.conf`. In that file:
```
    #stop the service gracefully if the runlevel changes to 'reboot'
    stop on runlevel [06]

    exec /volume1/homes/sean/node_exporter
```
After that you can test with a quick restart, and verify metrics are being exported by going to the ip, port, /metrics. In my case `192.168.143.250:9100/metrics`.
