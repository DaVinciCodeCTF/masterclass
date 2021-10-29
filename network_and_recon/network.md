# Network and Recon Masterclass from DaVinciCode

Clovis Carlier / Joytide - 27/10



## OSI Model

![Day 51: Understanding the OSI Model | by Z3R0 | Medium](https://miro.medium.com/max/1024/1*17Zz6v0HWIzgiOzQYmO6lA.jpeg)



## Wireshark

- Most used tool to:
  - Capture packet
  - Display network captures (-> Often very useful in Forensics!)

- Example: IP addr (Layer 3), MAC addr (Layer 2) + Network interfaces

```
ip a
```



### ARP

- Link between layer 2 and 3: assign a MAC to an IP

```bash
arp -a
arp -d 
arp -a
```



- Vulnerable to ARP Cache Poisoning (MiTM):

![ARP spoofing : attaques du réseau interne - IONOS](https://www.ionos.fr/digitalguide/fileadmin/DigitalGuide/Screenshots/arp_spoofing-example.png)



- Another common spoofing attack: MAC Poisoning	

![IP/MAC Spoofing Attack](https://download.huawei.com/mdl/imgDownload?uuid=d9b06515129041c0b1ff19fc6ed3bb51.png)





## Application Layer

### DHCP

- Dynamic Host Configuration Protocol

- Gives IP to client on a network

Can be done manually:

```bash
#Create a network interface
ifconfig eth0 192.168.43.226
ifconfig eth0 netmask 255.255.255.0
ifconfig eth0 broadcast 192.168.43.255
#Add it to routes
route add default gw 192.168.43.1 eth0
```



### DNS

- Domain Name System
- Often public and managed by big company (Google: 8.8.8.8 and 8.8.4.4), manually added to: ``/etc/resolv.conf``
- Assign an IP to a domain name

```bash
nslookup -type=txt dvc.tf 8.8.8.8
dig @8.8.8.8 dvc.tf TXT
```

- Record DNS: ANY, A, TXT...



##### DNS Spoofing/Cache poisonning/Hijacking

![What is DNS Spoofing | Cache Poisoning Attack Example | Imperva](https://www.imperva.com/learn/wp-content/uploads/sites/13/2019/01/DNS-spoofing.jpg)

##### Zone transfers:

```bash
dig axfr @$(DNS_IP) $(DOMAIN.COM)
```



## Reconnaissance

#### Nmap

Full in depth port scan

```bash
sudo nmap -sC -sV -oA box $(ip)
```

Full port scan

```bash
sudo nmap -p- -v -oA box_allports $(ip) # add --min-rate 1000/10000 if needed
```

In depth port scan :

```bash
cat allports.nmap | grep open | awk -F/ '{print $1}' | sed-z 's/\n/,/g;s/,$/\n/' #For open ports
sudo nmap -sC -sV -oA box $(ip) -p $(ports)
```

UDP port scan:

```bash
sudo nmap -sU -oA box_udp $(ip)
```



#### sTTL

```bash
ping $(ip)
# ttl<127 	=> Windows
# ttl<64 	=> Linux, BSD, IoT...
# ttl<256 	=> Network infrastructure, like a cisco router
```

##### Subdomain enumeration

```bash
gobuster vhost -u https://dvc.tf -w /opt/SecLists/Discovery/DNS/subdomains # For subdomains discovery as sub.domain.htb
```



