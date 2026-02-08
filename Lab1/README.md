# Lab 1: Linux Password Auditing with John the Ripper

## 📌 Overview
This repository documents the process of auditing Linux authentication security by performing a dictionary attack on local system hashes.

## 🛠️ Environment
- **Host OS:** Pop!_OS (Linux)
- **Virtualization:** QEMU/KVM (Virt-Manager)
- **Target OS:** Kali Linux 2025.4
- **Hardware:** Intel Core i3 4th Gen

## 🛡️ Technical Implementation
### Phase 1: Environment Setup
Using a QEMU image for resource efficiency, I deployed a Kali Linux instance. The instance was configured with 2GB RAM and 2 CPU threads to balance performance on 4th Gen hardware.

### Phase 2: Hash Extraction
I utilized the `unshadow` utility to combine `/etc/passwd` and `/etc/shadow`.
> **Note:** Modern Linux systems use the **yescrypt** ($y$) algorithm, which is memory-hard and computationally expensive to prevent brute-forcing.

### Phase 3: The Attack
Since the `rockyou.txt` list would take ~5 days on this hardware, a targeted dictionary attack was performed using the following command:
`john --format=crypt --wordlist=lab_passwords.txt myhashes.txt`

## 📊 Results
![Cracked Passwords](./screenshots/result_summary.png)
Successfully recovered plain-text passwords for `kali` and `labuser`.
