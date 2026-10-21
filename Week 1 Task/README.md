# Week 1 Task: Nmap Scanning with Metasploitable

## Overview

For this task, I set up a Kali Linux environment and used **Nmap** to perform reconnaissance against a Metasploitable virtual machine in my own lab. The objective was to identify the target, discover exposed TCP ports, and collect service and operating-system information.

> **Scope:** All scans documented here were performed against the intentionally vulnerable Metasploitable VM in a controlled lab environment.

## Lab Environment

- **Attacker machine:** Kali Linux
- **Target machine:** Metasploitable 2
- **Target IP used:** `192.168.191.130`
- **Tool:** Nmap 7.98
- **Virtualization/network:** VMware virtual machine network (based on the scan output)

## Tasks Performed

### 1. Started the lab machines

I started Kali Linux and logged in to the Metasploitable console. The console showed the Metasploitable login banner and a successful login as `msfadmin`.

![Kali Linux login screen](screenshots/kali-login.png)

![Metasploitable console login](screenshots/metasploitable-login.png)

### 2. Ran a basic Nmap scan

```bash
nmap 192.168.191.130
```

This scan checked Nmap's default set of common TCP ports and reported which were open. The captured output identified multiple exposed services, including FTP, SSH, Telnet, SMTP, DNS, HTTP, SMB, MySQL, PostgreSQL, VNC and others.

![Basic Nmap scan](screenshots/nmap-basic-scan.png)

### 3. Ran a TCP SYN scan

```bash
nmap -sS 192.168.191.130
```

The SYN scan checked common TCP ports using TCP SYN probes. In the captured output, Nmap reported the host as up and identified open TCP ports. This scan provided a port-discovery view without the service-version detail shown by the later scan.

![Nmap SYN scan](screenshots/nmap-syn-scan.png)

### 4. Detected service versions and operating-system details

```bash
sudo nmap -O -sV 192.168.191.130
```

- `-O` requests operating-system detection.
- `-sV` probes open ports to identify services and their versions.
- `sudo` provides the privileges commonly needed for OS detection and SYN-based techniques.

The captured output identified service/version information for several services and suggested a Linux 2.6.x system. OS fingerprinting is an estimate, not an absolute guarantee.

![Nmap OS and version detection](screenshots/nmap-os-version-detection.png)

## Key Findings

The screenshots show these open TCP ports and services on `192.168.191.130`:

| Port | Service identified | Security relevance |
|---:|---|---|
| 21/tcp | FTP (`vsftpd 2.3.4` in version scan) | Review FTP configuration and whether credentials or data are transmitted securely. |
| 22/tcp | SSH | Check server version, authentication policy and configuration. |
| 23/tcp | Telnet | Telnet is unencrypted; prefer SSH for remote administration. |
| 25/tcp | SMTP | Review mail-service configuration and relay restrictions. |
| 53/tcp | DNS | Review DNS configuration and exposed functionality. |
| 80/tcp | HTTP (Apache in version scan) | Enumerate web content and review server configuration. |
| 139, 445/tcp | NetBIOS/SMB | Review file-sharing exposure, permissions and SMB configuration. |
| 3306/tcp | MySQL | Restrict database access to trusted hosts and review authentication. |
| 5432/tcp | PostgreSQL | Restrict database access and review authentication/configuration. |
| 5900/tcp | VNC | Check access controls and avoid exposing remote desktop services unnecessarily. |
| 8009, 8180/tcp | AJP / Apache Tomcat-related services | Review whether these services need to be exposed and check their configurations. |

The scan also reported other open ports, including 111, 512–514, 1099, 1524, 2049, 2121, 6000 and 6667. Service labels and versions should be verified with follow-up enumeration where necessary.

## What I Learned

- How to identify a target host and scan common TCP ports with Nmap.
- How a SYN scan (`-sS`) differs from a default TCP scan.
- How service/version detection (`-sV`) adds context to open-port results.
- How OS detection (`-O`) provides a best-effort operating-system fingerprint.
- Why exposed services should be reviewed according to their purpose, configuration and access controls.
- Why scan results are evidence for further investigation, not proof by themselves that a host is exploitable.

## Suggested Follow-up

For additional practice in this isolated lab, I can save scan output for a report and perform targeted, non-destructive enumeration of selected services:

```bash
sudo nmap -sV -sC -p 21,22,23,25,53,80,139,445,3306,5432,5900,8009,8180 \
  -oA metasploitable-targeted 192.168.191.130
```

The command saves normal, XML and grepable output files. Review NSE scripts before using them, and run scans only on systems for which testing is authorized.

## Evidence

Screenshots are stored in the [`screenshots/`](screenshots/) directory and embedded above for easy review.
