---
layout: default
title: "Silver Platter Room - TryHackMe Walkthrough Meow!"
date: 2025-08-06
categories: cybersecurity tryhackme walkthrough penetration-testing
---

{% include post-navigation.html %}

<div style="text-align: center; margin: 20px 0;">
    <img src="https://images.unsplash.com/photo-1555066931-4365d14bab8c?ixlib=rb-4.0.3&auto=format&fit=crop&w=800&q=80" alt="TryHackMe cybersecurity challenge" style="max-width: 100%; height: 300px; object-fit: cover; border-radius: 10px;">
    <p style="font-style: italic; color: #666; margin-top: 10px;">BlueCat tackles TryHackMe's Silver Platter challenge, meow! 🐱⚡</p>
</div>

# Silver Platter Room - TryHackMe Walkthrough Meow! 🐱

*Originally published on [LinkedIn](https://www.linkedin.com/pulse/silver-platter-room-tryhackme-min-thu-eymdc/), adapted for BlueCat's cybersecurity blog with extra meow wisdom!*

Meow meow! Today BlueCat is sharing a detailed penetration test report of the TryHackMe "Silver Platter" room. This was a purrfect example of how multiple vulnerabilities can be chained together for full system compromise, meow!

## 📋 Penetration Test Report - Meow Edition!

**Target:** TryHackMe - Silver Platter  
**Assessment Type:** CTF-style engagement  
**Date:** July 9, 2025  
**Author:**Meow

### 🎯 1. Executive Summary

This assessment targeted the "Silver Platter" machine on TryHackMe, meow! The objective was to compromise the system and capture both user and root flags - like catching two mice with one pounce!

The system was vulnerable to multiple high-impact flaws:
- **Silverpeas authentication bypass** (CVE-2024-36042) 🚫
- **IDOR vulnerability** leaking internal messages (CVE-2023-47323) 📝  
- **Poor operational security** in log management and sudo configurations 🔓

**Outcome:** Full compromise achieved - BlueCat caught all the digital mice! 🐭  
**Risk Level:** Critical ⚠️

### 🎯 2. Scope

- **Target IP:** 10.10.****
- **Flag Goals:** `/home/*/user.txt`, `/root/root.txt`

### 🔍 3. Methodology - BlueCat's Hunting Strategy

The assessment followed a structured black-box testing approach - like a cat stalking its prey methodically, meow!

**Key tools and techniques:**
- **Rustscan and Nmap** for initial recon (sniffing around)
- **Wfuzz** for directory and parameter fuzzing (looking under every digital rock)
- **Burp Suite** for intercepting web requests (catching packets like flies!)
- **SSH** for shell access (entering the mouse hole)
- **Manual log review** for privilege escalation (finding hidden treasures)

### 🕵️ 4. Detailed Findings - The Hunt Begins!

#### 4.1 Initial Recon - Sniffing Out Services

```bash
rustscan -a 10.10.***.*** --ulimit 5000 -b 2500 -- -sC -sV -oA rustscan-full
```

**Discovered Ports:**
- `22/tcp` – OpenSSH 8.9p1 🔑
- `80/tcp` – nginx web server 🌐  
- `8080/tcp` – Mystery service 🤔

#### 4.2 Web Enumeration (Port 80) - Exploring the Territory

```bash
wfuzz -u http://10.10.***.***/FUZZ -w /opt/SecLists/Discovery/Web-Content/raft-small-directories.txt --hc 404 -t 50
```

**Endpoints Discovered:**
- `/contact` (revealed username `scr1ptkiddy` - meow, what a name!)
- `/about`, `/home`

#### 4.3 Silverpeas Enumeration (Port 8080) - The Plot Thickens

```bash
wfuzz -u http://10.10.***.***:8080/silverpeas/FUZZ -w /opt/SecLists/Discovery/Web-Content/raft-medium-words.txt --hc 404 -t 50
```

**Endpoints:** `/jsp`, `/RSILVERMAIL`

#### 4.4 Authentication Bypass - CVE-2024-36042 🚪

**Vector:** Login to Silverpeas with `scr1ptkiddy`, remove password parameter using Burp Suite.

**Result:** Bypassed login and granted SuperAdmin web access - meow! It was easier than opening a can of tuna!

> **BlueCat's Note:** This is why input validation is crucial, meow! Never trust what users send you - they might be sneaky cats trying to bypass your authentication!

#### 4.5 IDOR Vulnerability - CVE-2023-47323 📨

**URL Accessed:**
```
http://10.10.***.***:8080/silverpeas/RSILVERMAIL/jsp/ReadMessage.jsp?ID=6
```

**Leaked Credentials:**
- **Username:** `tim`
- **Password:** `cm0nt!**************************`

> **BlueCat's Wisdom:** IDOR vulnerabilities are like leaving your diary open - anyone can read messages that aren't meant for them, meow!

#### 4.6 Foothold via SSH - Entering the Castle! 🏰

```bash
ssh tim@10.10.***.***
```

**Flag Captured:** `/home/tim/user.txt`  
**Value:** `THM{c4***9b}` 🏁

#### 4.7 Privilege Escalation to Tyler - Climbing Higher! 🧗

**Inspected:** `/var/log/auth.log`

**Found:** `DB_PASSWORD=_Zd*****/`

**Escalation Path:**
```bash
su tyler
# Password: _Zd*****/
sudo -l  # Full sudo access - jackpot!
sudo su
cat /root/root.txt
```

**Root Flag:** `THM{09****f6}` 🎉

### 🔗 5. Exploit Path Summary - The Full Hunt

1. **Web Enumeration** → `/contact` leaks `scr1ptkiddy` username
2. **Silverpeas Bypass** → CVE-2024-36042 allows admin access  
3. **IDOR Abuse** → CVE-2023-47323 leaks tim's SSH credentials
4. **SSH Foothold** → Login as tim, read user flag
5. **Log Abuse** → DB password reused for tyler user
6. **Sudo Exploitation** → `sudo su` to root, read root flag

### 🛡️ 6. BlueCat's Security Recommendations - Protecting the Digital Territory

To address these vulnerabilities, meow meow:

1. **Upgrade Silverpeas** to version 6.3.5+ to patch CVE-2024-36042
2. **Implement proper access controls** - don't let cats read other cats' messages!
3. **Never log sensitive credentials** - it's like writing your secrets on the wall, meow!
4. **Apply principle of least privilege** - not every cat needs root access!
5. **Regular security audits** - keep your claws sharp and your defenses sharper!

### 🏁 7. Conclusion - Mission Accomplished!

The target system was successfully compromised using multiple real-world vulnerabilities, meow! This challenge perfectly demonstrates how security flaws can be chained together like a cat toy on a string.

**Key Lessons:**
- **Defense in depth** is crucial - one weak link can compromise everything
- **Logging practices** matter - don't accidentally expose secrets
- **Access controls** should be properly implemented and audited
- **Regular updates** are essential to patch known vulnerabilities

Remember: A secure system is like a well-protected cat fortress - multiple layers of defense and constant vigilance, meow meow! 🏰🐱

---

**Ready to try this challenge yourself, meow?** Visit [TryHackMe](https://tryhackme.com) and sharpen your cybersecurity claws! Don't forget to practice responsible disclosure and ethical hacking principles! 🐾

**What do you think of this writeup, meow?** Share your thoughts and stay secure online! 

{% include post-navigation.html %}
