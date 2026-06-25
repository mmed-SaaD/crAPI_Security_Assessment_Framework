**└─$ nmap -sV -O -sC -sS 192.168.100.15**
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-24 08:00 -0400
Nmap scan report for 192.168.100.15
Host is up (0.0012s latency).
Not shown: 992 closed tcp ports (reset)
PORT     STATE SERVICE         VERSION
**8443/tcp open  ssl/http        OpenResty web app server 1.27.1.2**
|_http-server-header: openresty/1.27.1.2
| **tls-alpn:** 
|   http/1.1
|   http/1.0
|_  http/0.9
| **ssl-cert: Subject: organizationName=CompanyName/stateOrProvinceName=StateName/countryName=XX**
| Not valid before: 2025-07-05T09:29:39
|_Not valid after:  2035-07-03T09:29:39
|_**http-title: crAPI**
|_ssl-date: TLS randomness does not represent time
**8888/tcp open  http            OpenResty web app server 1.27.1.2**
|_http-server-header: openresty/1.27.1.2
**|_http-title: crAPI**
MAC Address: **MAC Hidden**
Device type: general purpose
Running: Microsoft Windows 11
OS CPE: cpe:/o:microsoft:windows_11
OS details: Microsoft Windows 11 24H2
Network Distance: 1 hop
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2026-06-24T12:01:04
|_  start_date: N/A
|_nbstat: NetBIOS name: BATCOMPUTER, NetBIOS user: <unknown>, NetBIOS MAC: 00:45:e2:0f:74:b7 (CyberTAN Technology)