Issue: unable to SSH into server after power outage or reboot due to LUKS encryption prompting for a password in init at boot. 

Proposed solution: Use Dropbear SSH keys 

Steps taken: 

installing dropbear

edit /etc/dropbear-iniramfs/conf to include timeout options, disable ssh local port forwarding, disable remote port fowarding, listen Dropbear ssh server, and disable password logins (set up SSH keys ... 
DROPBEAR_OPTIONS="-I 180 -j -k -p 2222 -s" 


edit /etc/dropbear-iniramfs/initramfs.conf file to include the server static IP info... 
IP=192.168.1.50::192.168.1.1::255.255.255.0:myliavielos






# Equipment

DOCSIS 3.1 Modem from ISP

Ubiquiti Edgerouter-X v2.0.9-hotfix.4

Cisco Catalyst 2960G-8TC-L

Cisco Catalyst 3560CG-8PC-S

Ubiquiti UniFi 6 Lite WAP PoE

Ubiquiti Unifi Video G3 Camera PoE

Ivy Bridge machine running Ubuntu Server 22.04 LTS

Samsung UN75JU7100

Sonos Playbar, 2x Play:3, 2x Play:1, 1x Connect:Amp
