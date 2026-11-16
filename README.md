# Footprint
# Lab 4: Active Reconnaissance (Footprinting)

- **Name:** MUHAMMAD ASYIFF HAKIMI BIN JAMALI
- **Student ID:** 52215226012
- **Group:** L01-B03



---

## Scope

For this lab I only scanned my own vulnerable machine, Bluemoon from VulnHub. Both my attacker and the target run inside VirtualBox on the same private Host-Only network, so no public systems were ever touched. I kept everything to active reconnaissance, which means host discovery together with port and service scanning. I did not do any exploitation, brute forcing, persistence or destructive testing.

---

## 1. Target Setup (Victim) and Attacker

Both machines use the VirtualBox Host-Only Adapter, which keeps all of their traffic on one private, isolated segment (`192.168.56.0/24`).

| Role | VM | Adapter | MAC | IP |
|------|-----|---------|-----|-----|
| Attacker | Kali Linux | Host-Only | `# Lab 4: Active Reconnaissance (Footprinting)



## Scope

For this lab I only scanned my own vulnerable machine, Bluemoon from VulnHub. Both my attacker and the target run inside VirtualBox on the same private Host-Only network, so no public systems were ever touched. I kept everything to active reconnaissance, which means host discovery together with port and service scanning. I did not do any exploitation, brute forcing, persistence or destructive testing.

---

## 1. Target Setup (Victim) and Attacker

Both machines use the VirtualBox Host-Only Adapter, which keeps all of their traffic on one private, isolated segment (`192.168.56.0/24`).

| Role | VM | Adapter | MAC | IP |
|------|-----|---------|-----|-----|
| Attacker | Kali Linux | Host-Only | `# Lab 4: Active Reconnaissance (Footprinting)

---

## Scope

For this lab I only scanned my own vulnerable machine, Bluemoon from VulnHub. Both my attacker and the target run inside VirtualBox on the same private Host-Only network, so no public systems were ever touched. I kept everything to active reconnaissance, which means host discovery together with port and service scanning. I did not do any exploitation, brute forcing, persistence or destructive testing.

---

## 1. Target Setup (Victim) and Attacker

Both machines use the VirtualBox Host-Only Adapter, which keeps all of their traffic on one private, isolated segment (`192.168.56.0/24`).

| Role | VM | Adapter | MAC | IP |
|------|-----|---------|-----|-----|
| Attacker | Kali Linux | Host-Only | `08:00:27:5A:87:BC` | `192.168.56.103` |
| Target | Bluemoon (VulnHub) | Host-Only | `08:00:27:B9:66:CC` | `192.168.56.102` |


**Target confirmation:** I did not simply assume which host was Bluemoon. In its VirtualBox settings the target's adapter MAC is `08:00:27:B9:66:CC`, and that is the same MAC Nmap reported for `08:00:27:B9:66:CC` in both scans below. Because the two match, I am confident that `192.168.56.102` really is Bluemoon.

---

## 2. Own Host (Attacker) and Host Discovery

My Kali attacker sits at `192.168.56.103/24`. There is no default route in its routing table, only the kernel link-scope route for the local subnet, which confirms the network is isolated with no gateway out.



A ping sweep of the subnet found four live hosts. These were `.1` (the Host-Only adapter), `.100` (the VirtualBox DHCP server), `.103` (Bluemoon, my target) and `.102` (my own Kali machine).



---

## 3. Findings, Evidence and Limitations

### Findings

Service and version scan of `192.168.56.102`:

| Port | State | Service | Version (from banner) |
|------|-------|---------|-----------------------|
| 21/tcp | open | ftp | vsftpd 3.0.3 |
| 22/tcp | open | ssh | OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0) |
| 80/tcp | open | http | Apache httpd 2.4.38 ((Debian)) |

The other 997 ports came back as closed with a reset, which means the host actively refuses them instead of quietly dropping the packets. That tells me there is no packet-filtering firewall sitting in front of these services.

Overall, the target looks like a Debian 10 (Buster) Linux host running an FTP server, SSH and a web server, and all three version banners line up with the stock Debian 10 packages.

### Evidence


Everything above comes straight from my own scan output, produced by Nmap 7.99 running on my Kali host (`192.168.56.102`).

### Interpretation and Confidence

Ports 21, 22 and 80 are open, and their banners point to vsftpd 3.0.3, OpenSSH 7.9p1 and Apache 2.4.38, which all fit a Debian 10 host. My confidence here is medium, because these versions are what the services report about themselves and I have not independently verified them.

I am deliberately not calling any of these services vulnerable. Debian tends to backport security fixes while keeping the old version number, so a banner that looks outdated is not on its own proof that something can be exploited. It is also worth noting that vsftpd 3.0.3 is a normal release and not the backdoored 2.3.4 version that people often worry about.

### Limitations

- Service banners can be changed or faked, so a reported version is only an indicator and not solid proof.
- A version that looks outdated is not the same as a confirmed vulnerability, and I did not validate or exploit anything.
- I only scanned the top 1000 TCP ports that `nmap -sV` checks by default. I did not run a full port scan or any UDP scan, so there could be other open ports I did not see.
- The results only describe the target at the moment I scanned it.

---

## Commands I used

```bash
# Attacker own-host details
ip -br addr
ip route

# Host discovery on the Host-Only segment
sudo nmap -sn 192.168.56.0/24

# Service and version scan of the target
sudo nmap -sV 192.168.56.101
```` | `192.168.56.102` |
| Target | Bluemoon (VulnHub) | Host-Only | `08:00:27:6A:07:91` | `192.168.56.101` |

![Lab setup, attacker and target on Host-Only](01-lab-setup.png)

**Target confirmation:** I did not simply assume which host was Bluemoon. In its VirtualBox settings the target's adapter MAC is `08:00:27:6A:07:91`, and that is the same MAC Nmap reported for `192.168.56.101` in both scans below. Because the two match, I am confident that `192.168.56.101` really is Bluemoon.

---

## 2. Own Host (Attacker) and Host Discovery

My Kali attacker sits at `192.168.56.102/24`. There is no default route in its routing table, only the kernel link-scope route for the local subnet, which confirms the network is isolated with no gateway out.

![Attacker ip addr and ip route](02-ip-addr.png)

A ping sweep of the subnet found four live hosts. These were `.1` (the Host-Only adapter), `.100` (the VirtualBox DHCP server), `.101` (Bluemoon, my target) and `.102` (my own Kali machine).

![Host discovery with nmap ping sweep](03-nmap-sn.png)

---

## 3. Findings, Evidence and Limitations

### Findings

Service and version scan of `192.168.56.101`:

| Port | State | Service | Version (from banner) |
|------|-------|---------|-----------------------|
| 21/tcp | open | ftp | vsftpd 3.0.3 |
| 22/tcp | open | ssh | OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0) |
| 80/tcp | open | http | Apache httpd 2.4.38 ((Debian)) |

The other 997 ports came back as closed with a reset, which means the host actively refuses them instead of quietly dropping the packets. That tells me there is no packet-filtering firewall sitting in front of these services.

Overall, the target looks like a Debian 10 (Buster) Linux host running an FTP server, SSH and a web server, and all three version banners line up with the stock Debian 10 packages.

### Evidence

![Service and version scan with nmap -sV](04-nmap-sV.png)

Everything above comes straight from my own scan output, produced by Nmap 7.99 running on my Kali host (`192.168.56.102`).

### Interpretation and Confidence

Ports 21, 22 and 80 are open, and their banners point to vsftpd 3.0.3, OpenSSH 7.9p1 and Apache 2.4.38, which all fit a Debian 10 host. My confidence here is medium, because these versions are what the services report about themselves and I have not independently verified them.

I am deliberately not calling any of these services vulnerable. Debian tends to backport security fixes while keeping the old version number, so a banner that looks outdated is not on its own proof that something can be exploited. It is also worth noting that vsftpd 3.0.3 is a normal release and not the backdoored 2.3.4 version that people often worry about.

### Limitations

- Service banners can be changed or faked, so a reported version is only an indicator and not solid proof.
- A version that looks outdated is not the same as a confirmed vulnerability, and I did not validate or exploit anything.
- I only scanned the top 1000 TCP ports that `nmap -sV` checks by default. I did not run a full port scan or any UDP scan, so there could be other open ports I did not see.
- The results only describe the target at the moment I scanned it.

---

## Commands I used

```bash
# Attacker own-host details
ip -br addr
ip route

# Host discovery on the Host-Only segment
sudo nmap -sn 192.168.56.0/24

# Service and version scan of the target
sudo nmap -sV 192.168.56.101
```` | `192.168.56.103` |
| Target | Bluemoon (VulnHub) | Host-Only | `08:00:27:6A:07:91` | `192.168.56.102` |


**Target confirmation:** I did not simply assume which host was Bluemoon. In its VirtualBox settings the target's adapter MAC is `08:00:27:6A:07:91`, and that is the same MAC Nmap reported for `192.168.56.101` in both scans below. Because the two match, I am confident that `192.168.56.101` really is Bluemoon.

---

## 2. Own Host (Attacker) and Host Discovery

My Kali attacker sits at `192.168.56.102/24`. There is no default route in its routing table, only the kernel link-scope route for the local subnet, which confirms the network is isolated with no gateway out.



A ping sweep of the subnet found four live hosts. These were `.1` (the Host-Only adapter), `.100` (the VirtualBox DHCP server), `.101` (Bluemoon, my target) and `.102` (my own Kali machine).


---

## 3. Findings, Evidence and Limitations

### Findings

Service and version scan of `192.168.56.101`:

| Port | State | Service | Version (from banner) |
|------|-------|---------|-----------------------|
| 21/tcp | open | ftp | vsftpd 3.0.3 |
| 22/tcp | open | ssh | OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0) |
| 80/tcp | open | http | Apache httpd 2.4.38 ((Debian)) |

The other 997 ports came back as closed with a reset, which means the host actively refuses them instead of quietly dropping the packets. That tells me there is no packet-filtering firewall sitting in front of these services.

Overall, the target looks like a Debian 10 (Buster) Linux host running an FTP server, SSH and a web server, and all three version banners line up with the stock Debian 10 packages.

### Evidence

![Service and version scan with nmap -sV](04-nmap-sV.png)

Everything above comes straight from my own scan output, produced by Nmap 7.99 running on my Kali host (`192.168.56.102`).

### Interpretation and Confidence

Ports 21, 22 and 80 are open, and their banners point to vsftpd 3.0.3, OpenSSH 7.9p1 and Apache 2.4.38, which all fit a Debian 10 host. My confidence here is medium, because these versions are what the services report about themselves and I have not independently verified them.

I am deliberately not calling any of these services vulnerable. Debian tends to backport security fixes while keeping the old version number, so a banner that looks outdated is not on its own proof that something can be exploited. It is also worth noting that vsftpd 3.0.3 is a normal release and not the backdoored 2.3.4 version that people often worry about.

### Limitations

- Service banners can be changed or faked, so a reported version is only an indicator and not solid proof.
- A version that looks outdated is not the same as a confirmed vulnerability, and I did not validate or exploit anything.
- I only scanned the top 1000 TCP ports that `nmap -sV` checks by default. I did not run a full port scan or any UDP scan, so there could be other open ports I did not see.
- The results only describe the target at the moment I scanned it.

---

## Commands I used

```bash
# Attacker own-host details
ip -br addr
ip route

# Host discovery on the Host-Only segment
sudo nmap -sn 192.168.56.102/24

# Service and version scan of the target
sudo nmap -sV 192.168.56.102
```
