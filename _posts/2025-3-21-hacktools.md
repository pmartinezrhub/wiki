---
title: HackTools
date: 2025-03-21 19:00:00 +0200
categories: [hacking, herramientas]
tags: [hacking, herramientas]     # TAG names should always be lowercase
image:
    path: https://upload.wikimedia.org/wikipedia/commons/thumb/4/4c/Coding_da_Vinci_-_Der_Kultur-Hackathon_%2814123515524%29.jpg/1280px-Coding_da_Vinci_-_Der_Kultur-Hackathon_%2814123515524%29.jpg
    alt: Hackers
---

This document is updated, modified when i notice a tool is deprecated or a new tool is tested. 


### General Security Info
-  OWASP [https://owasp.org](https://owasp.org/)
-  Exploitdb [https://gitlab.com/exploit-database/exploitdb](https://gitlab.com/exploit-database/exploitdb) Archive of public exploits
-  Hacking articles [https://github.com/Ignitetechnologies](https://github.com/Ignitetechnologies) Lots of hacking articles

### General propouse tools 
- Netcat [https://netcat.sourceforge.net/](https://netcat.sourceforge.net/) Called the swiss army knife (for hackers)
- Socat [https://www.dest-unreach.org/socat/](https://www.dest-unreach.org/socat/) Netcat on steroids
- Curl [https://curl.haxx.se/](https://curl.haxx.se/) cURL is a command-line tool used for transferring data to and from servers using - - various protocols like HTTP, HTTPS, FTP, and more.
- Seclist [https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists) It's a collection of multiple types of lists used during security assessments

### Network scanners 
- Netdiscover [https://github.com/netdiscover-scanner/netdiscover](https://github.com/netdiscover-scanner/netdiscover)Network address discovering tool 
- Nmap [https://nmap.org/](https://nmap.org/) network port scanner
- Zmap [https://zmap.io/](https://zmap.io/) ZMap is a fast single-packet network scanner 
- Nmap-scripts [https://github.com/cldrn/nmap-nse-scripts](https://github.com/cldrn/nmap-nse-scripts) Collection of nmap scripts 
- SSLScan [https://github.com/rbsec/sslscan](https://github.com/rbsec/sslscan) Queries SSL/TLS services (such as HTTPS)

### Vulnerability scanners 
- Openvas [https://www.openvas.org/](https://www.openvas.org/)A discover vulns application
- Nessus [https://www.tenable.com/products/nessus-vulnerability-scanner](https://www.tenable.com/products/nessus-vulnerability-scanner)- - Another vulns discover
- Nuclei [https://github.com/projectdiscovery/nuclei](https://github.com/projectdiscovery/nuclei)

### Web browsers &amp;  addons 
- Firefox [https://www.mozilla.org](https://www.mozilla.org)Browser
- Wappalyzer  [https://www.wappalyzer.com/](https://www.wappalyzer.com/)This extensión identifies technologies on a web
- FoxyProxy [https://getfoxyproBEST](https://getfoxyproBEST)Proxy switcher for browsers.

### Web pentest frameworks 
- Burpsuite [https://portswigger.net/burp](https://portswigger.net/burp)(Free version limited)
- Zap Attack Proxy [https://www.zaproxy.org/](https://www.zaproxy.org/)
- W3af [https://w3af.sourceforge.net/](https://w3af.sourceforge.net/)
- Nitkto [https://www.cirt.net/nikto2](https://www.cirt.net/nikto2)
- Whatweb [https://www.whatweb.net](https://www.whatweb.net)Next generation web scanner 
- WafW00f [https://github.com/EnableSecurity/wafw00f](https://github.com/EnableSecurity/wafw00f)WAF detector 
- Lbd [https://github.com/hacktrackgnulinux/lbd](https://github.com/hacktrackgnulinux/lbd)Load balancer detector 

### Web payloads 
- PayloadsAllTheThings [https://github.com/swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) A list of useful payloads and bypasses for Web Application Security.

#### Web CMS utils
- Cmseek [https://gitlab.com/kalilinux/packages/cmseek](https://gitlab.com/kalilinux/packages/cmseek)
- Wpscan [https://wpscan.com/](https://wpscan.com/) Scan vulns for Wordpress
- Joomscan [https://github.com/OWASP/joomscan](https://github.com/OWASP/joomscan) Scan vulns for Joomla

### Fuzzers 
- Wfuzz [https://www.edge-security.com/wfuzz.php](https://www.edge-security.com/wfuzz.php)
- Gobuster [https://github.com/OJ/gobuster](https://github.com/OJ/gobuster)

### Bruteforce 
- THCHydra [https://www.thc.org/thc-hydra/](https://www.thc.org/thc-hydra/)

### SQL Injection 
- SQLi Payloads [https://github.com/payloadbox/sql-injection-payload-list](https://github.com/payloadbox/sql-injection-payload-list)
- Sqlmap [https://github.com/sqlmapproject/sqlmap/wiki](https://github.com/sqlmapproject/sqlmap/wiki)Automatic SQLi

### XSS Cross Site Scripting 
- XSStrike [https://github.com/s0md3v/XSStrike](https://github.com/s0md3v/XSStrike)XSStrike is a Cross Site Scripting detection suite 
- Portswigger cheat-sheet for XSS [https://portswigger.net/web-security/cross-site-scripting/cheat-sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)

### Exploiting  
- Metasploit [https://www.metasploit.com/](https://www.metasploit.com/)
- Armitage [https://www.fastandeasyhacking.com](https://www.fastandeasyhacking.com)
- BeEF [https://beefproject.com/](https://beefproject.com/)BeEF is short for The Browser Exploitation Framework. It is a penetration testing tool that focuses on the web browser.  
- NetExec [https://github.com/Pennyw0rth/NetExec](https://github.com/Pennyw0rth/NetExec) Network service exploitation tool
- Evil-Winrm [https://github.com/Hackplayers/evil-winrm](https://github.com/Hackplayers/evil-winrm)The ultimate WinRM shell for hacking/pentesting
- Responder [https://github.com/SpiderLabs/Responder](https://github.com/SpiderLabs/Responder) Responder an LLMNR, NBT-NS and MDNS poisoner
- Impacket [https://github.com/fortra/impacket](https://github.com/fortra/impacket) Impacket is a collection of Python classes for working with network protocols
- revshell [https://www.revshells.com/](https://www.revshells.com/) Reverse shells generator

### Web Shells 
- r57shell [https://github.com/r57shell](https://github.com/r57shell)
- c99shell [https://github.com/phpwebshell/c99shell](https://github.com/phpwebshell/c99shell)
- Weevely3 [https://github.com/epinna/weevely3](https://github.com/epinna/weevely3)

### DDoS 
- Ufonet [https://github.com/epsylon/ufonet](https://github.com/epsylon/ufonet)DDoS over TOR
- Hping [https://www.hping.org/](https://www.hping.org/)Stress

### OSINT 
- Shodan [https://www.shodan.io/](https://www.shodan.io/)
- Google [https://google.com](https://google.com)
- Sherlock [https://github.com/sherlock-project/sherlock](https://github.com/sherlock-project/sherlock)

### Social Engineer 
- Social Engineer toolkit [https://github.com/trustedsec/social-engineer-toolkit](https://github.com/trustedsec/social-engineer-toolkit)

### Networks 
- Openvpn [https://openvpn.net/](https://openvpn.net/)OpenVPN
- Tor [https://torproject.org/](https://torproject.org/)The Onion Router
- I2p [https://geti2p.net/en/](https://geti2p.net/en/) I2P Network
- Onioncat [https://www.onioncat.org/](https://www.onioncat.org/)VPN over TOR
- Macchanger [https://github.com/alobbs/macchanger](https://github.com/alobbs/macchanger)

### Wifi 
- Aircrack [https://www.aircrack-ng.org/](https://www.aircrack-ng.org/)
- PixieWPS [https://github.com/wiire-a/pixiewps](https://github.com/wiire-a/pixiewps)WPS PIN exploiting
- Reaver [https://github.com/tiagob0b/Reaver-WPS](https://github.com/tiagob0b/Reaver-WPS)WPS wireless bruteforce 
- Wifiphisher [https://github.com/wifiphisher/wifiphisher](https://github.com/wifiphisher/wifiphisher)Phising , Evil Twin, portal captive. 
- Fluxion [https://github.com/FluxionNetwork/fluxion](https://github.com/FluxionNetwork/fluxion)retrieve the WPA/WPA2 key from a target access point by means of a social engineering (phishing) attack 

### Router 
- RouterSploit [https://github.com/threat9/routersploit](https://github.com/threat9/routersploit)

### Packet inspectors 
- Wireshark [https://www.wireshark.org/](https://www.wireshark.org/)
- TCPDump [https://www.tcpdump.org/](https://www.tcpdump.org/)

### IDS 
- Suricata [https://suricata.io/](https://suricata.io/)
- Snort [https://www.snort.org](https://www.snort.org)

### Firewall 
- OpenSense [https://opnsense.org](https://opnsense.org)
- IPFire [https://www.ipfire.org/](https://www.ipfire.org/)

### Encoding and ciphers: 
- https://decode.frdecode.fr[https://www.dcode.fr/en](https://www.dcode.fr/en)Web with decrypters for all kind of ciphers, included root13, caesar, etc

### SQL Injection 
- Sql payloads [https://github.com/payloadbox/sql-injection-payload-list](https://github.com/payloadbox/sql-injection-payload-list)
- Sqlmap [https://github.com/sqlmapproject/sqlmap/wiki](https://github.com/sqlmapproject/sqlmap/wiki)

### SSTI
- SSTImap [https://github.com/vladko312/sstimap](https://github.com/vladko312/sstimap) SSTImap is a penetration testing software that can check websites for Code Injection and Server-Side Template Injection

### Reverse Engineering 
- IDA Free [https://hex-rays.com/ida-free](https://hex-rays.com/ida-free)
- Radare2 [https://www.radare.org/r/index.html](https://www.radare.org/r/index.html)

### Phising 
- Httrack [https://www.httrack.com/](https://www.httrack.com/)Website copier 
- Gophish [https:////github.com/gophish/gophish](https://github.com/gophish/gophish)Phising Framework

### Password Cracking 
- Hashcat [https://hashcat.net/wiki/](https://hashcat.net/wiki/)Cracking passwords tool
- Crunch [https://github.com/crunchsec/crunch](https://github.com/crunchsec/crunch)Dictionary generator
- Cuup [https://github.com/Mebus/cupp](https://github.com/Mebus/cupp)Common User Passwords Profiler
- Hash-identifier [https://gitlab.com/kalilinux/packages/hash-identifier](https://gitlab.com/kalilinux/packages/hash-identifier)Software to identify the different types of hashes used to encrypt data and especially passwords.
- Jwtcrack [https://github.com/Sjord/jwtcrack](https://github.com/Sjord/jwtcrack)Crack a HS256, HS384 or HS512-signed JWT

## Dedicated cibersecurity Linux distros

### Privacy
- Tails [https://tails.net](https://tails.net) A portable operating system that protects against surveillance and censorship. 
- Whonix [https://www.whonix.org](https://www.whonix.org) an anonymous operating system that runs like an app and routes all Internet traffic through the Tor anonymity network. 

### Forensic 
- Caine Linux [https://www.caine-live.net/](https://www.caine-live.net/)

### Pentest 
- Kali Linux [https://www.kali.org/](https://www.kali.org/)
- BackBox [https://www.backbox.org/](https://www.backbox.org/)
- BlackArch [https://www.blackarch.org//](https://www.blackarch.org/)

### Cyberdefense 
- Kali Purple [https://www.kali.org/](https://www.kali.org/)

### Security in general 
- Parrot OS [https://www.parrotsec.org/](https://www.parrotsec.org/)
- Qbes OS [https://www.qubes-os.org/](https://www.qubes-os.org/)

## Training

### Vulnerable machines 
- Vulnhub [https://www.vulnhub.com/](https://www.vulnhub.com/)A collection of vulnerable machines
- DVWA [https://github.com/digininja/DVWA](https://github.com/digininja/DVWA)Damn Vulnerable Web Application (DVWA) 
- Metasploitable [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)
- Metasploitable2 [https://downloads.metasploit.com/data/metasploitable/metasploitable-linux-2.0.0.zip](https://downloads.metasploit.com/data/metasploitable/metasploitable-linux-2.0.0.zip)
- Metasploitable3 [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3)
- WebGoat [https://github.com/WebGoat/WebGoat](https://github.com/WebGoat/WebGoat)WebGoat is a deliberately insecure web application maintained by OWASP designed to teach web application security lessons.

### CTFs 
- PicoCTF [https://picoctf.org/](https://picoctf.org/)
- Ctf.hacker101 [https://ctf.hacker101.com/](https://ctf.hacker101.com/)
- Hackthebox [https://www.hackthebox.com/](https://www.hackthebox.com/)
