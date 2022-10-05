Issue: unable to SSH into server after power outage or reboot due to LUKS encryption prompting for a password in init at boot. 

Proposed solution: Use Dropbear SSH keys 

Steps taken: 



installing dropbear

edit /etc/dropbear-iniramfs/conf to include timeout options, disable ssh local port forwarding, disable remote port fowarding, listen Dropbear ssh server, and disable password logins 

DROPBEAR_OPTIONS="-I 180 -j -k -p 2222 -s" 


edit /etc/dropbear-iniramfs/initramfs.conf file to include the server static IP info... 

IP=192.168.1.50::192.168.1.1::255.255.255.0:myliavielos

update-initramfs -u -v

generate SSH key pair

#in this case host machine is Windows

OpenSSH Client

mkdir -p $HOME/.ssh

#hope that the permissions are fine otherwise do it with icacls


ssh-keygen -t ed25519 -C "My key for Myliavielos server #42"

#rsa vs ed25519?

cp path\id_ed25519.pub path\authorized_keys


client: scp .\authorized_keys av@myliavielos:~/.ssh

#fail at copying public keys to /etc/dropbear-initramfs/authorized_keys

client: scp .\authorized_keys av@myliavielos:~/key.pub

client: ssh av@myliavielos

sudo -i

cat /home/av/key.pub >> /etc/dropbear-initramfs/authorized_keys

rm /home/av/key.pub
exit x2

sudo update-initramfs -u
 
!!! Invalid authorized_keys file, SSH login to initramfs wont work!

failed with rsa key too

 
 Ignoring error
 
 Attempting to ssh into server after reboot 
 
 > PS C:\WINDOWS\system32> ssh -vvv root@192.168.1.52 -i C:\Users\Me\.ssh\id_ed25519 -p 2222
 
> OpenSSH_for_Windows_8.1p1, LibreSSL 3.0.2

> debug3: Failed to open file:C:/Users/Me/.ssh/config error:2

> debug3: Failed to open file:C:/ProgramData/ssh/ssh_config error:2

> debug2: resolve_canonicalize: hostname 192.168.1.52 is address

> debug2: ssh_connect_direct

> debug1: Connecting to 192.168.1.52 [192.168.1.52] port 2222.

> debug3: finish_connect - ERROR: async io completed with error: 10061, io:000001D1A6D4F820

> debug1: connect to address 192.168.1.52 port 2222: Connection refused

> ssh: connect to host 192.168.1.52 port 2222: Connection refused

In attempts to troubleshoot, changed permissions of config and authentican_keys files and folders, 

Added config file to Windows ~/.ssh/ specifying several arguements 

Added client pub keys to /root/.ssh/authorized_keys

Encountered 'man in the middle' warning, so deleted known_hosts files


Managed to connect successfully once with ssh -i C:\Users\Me\.ssh\id_ed25519 -p 22 -o "HostKeyAlgorithms ssh-ed25519" av@192.168.1.51

Unfortunately it wasnt repeatable after reboot, server was not set to static. Setting the machine to static IP and working back to the previous steps including removing the known_hosts file that had worked before only resulted in getting permissions denied after every proceeding attempt. 

For ubuntu 22.04 there seems to be discrepancies in the location of some config and key files and different folder structure (dropbear-initramfs/config vs dropbear/initramfs/dropbear.conf), thus why the initial DROPBEAR-OPTIONS= line in the config file was being ignored. 

The entry IP=192.168.1.50::192.168.1.1::255.255.255.0:myliavielos was intentionally commented out due to the server being DHCP, attempts to uncomment the line resulted in no network connection at boot, rendering SSH attempts futile. 

Some users in the comments of https://hamy.io/post/0009/how-to-install-luks-encrypted-ubuntu-18.04.x-server-and-enable-remote-unlocking/ reported issues with Busybox (that hadnt prompted me in the one successful ssh login attempt with dropbear), buggy dropbear-initramfs package, experiencing network problems that werent alway able to have work-arounds, vlan incompatibilties with initramfs tools requring further workarounds, additional 3rd party fixes, etc, and most recent writeups are still significantly out of date in my findings. 

Future need for remote access with SSH keys will likely be instead dealt with as a physical usb SSH key, or network-based key. Without a specific use case for the LUKS encryption and dropbear to bypass it remotely, the experiment is over for now. 

Conclusion: Good learning experience and exercise in troubleshooting issues as they arose, ultimately turned into a task of growing complexity that exceeded the allocated time investment for a hobbyist, perhaps worth a revist if the need for the niche specialization arises. 

 
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
