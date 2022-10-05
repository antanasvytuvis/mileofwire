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
 
 PS C:\WINDOWS\system32> ssh -vvv root@192.168.1.52 -i C:\Users\Me\.ssh\id_ed25519 -p 2222
 
OpenSSH_for_Windows_8.1p1, LibreSSL 3.0.2

debug3: Failed to open file:C:/Users/Me/.ssh/config error:2

debug3: Failed to open file:C:/ProgramData/ssh/ssh_config error:2

debug2: resolve_canonicalize: hostname 192.168.1.52 is address

debug2: ssh_connect_direct

debug1: Connecting to 192.168.1.52 [192.168.1.52] port 2222.

debug3: finish_connect - ERROR: async io completed with error: 10061, io:000001D1A6D4F820

debug1: connect to address 192.168.1.52 port 2222: Connection refused

ssh: connect to host 192.168.1.52 port 2222: Connection refused


 
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
