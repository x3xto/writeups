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

And yes! I was going deeper an deeper into a `/r/a/b/b/i/t` hole.





