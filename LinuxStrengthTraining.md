## [[TryHackMe]](https://tryhackme.com/room/linuxstrengthtraining) Linux Strength Training room

### Task 2
What is the correct option for finding files based on group
+ `-group`
  
What is format for finding a file with the user named Francis and with a size of 52 kilobytes in the directory /home/francis/
+ `find /home/francis -type f -user francis -size 52k`

SSH as topson using his password topson. Go to the /home/topson/chatlogs directory and type the following: grep -iRl 'keyword'. What is the name of the file that you found using this command?
```
cd /home/topson/chatlogs
grep -iRl 'keyword'
```
+ 2019-10-11

Type: less [filename] to open the file. Then, before anything, type / before typing: keyword followed by [ENTER]. Notice how that allowed us to search for the first instance of that word in the entire document. For much larger documents this can be useful and if there are many more instances of that word in the document, we would be able to hit enter again to find the next instance in the document.
What are the characters subsequent to the word you found?

`less 2019-10-11`
+ ttitor

![image](https://github.com/user-attachments/assets/10aeeaab-5d5f-49e9-9a18-7e099496608d)

Read the file named 'ReadMeIfStuck.txt'. What is the Flag?

![image](https://github.com/user-attachments/assets/e48097a1-f2f0-4f0f-9155-5ff1407a58f9)
![image](https://github.com/user-attachments/assets/421e01a8-5ae8-4968-930e-5be6bbb0d06a)

```
cat ReadMeIfStuck.txt 
find / -type f -name additionalHINT 2>/dev/null
cat /home/topson/channels/additionalHINT
find / -type d -name 'telephone numbers' 2>/dev/null
ls '/home/topson/corperateFiles/xch/telephone numbers'
cat '/home/topson/corperateFiles/xch/telephone numbers/readME.txt'
find / -type d -name workflows 2>/dev/null
find /home/topson/workflows -type f -newermt 2016-09-11 ! -newermt 2016-09-13 2>/dev/null
```

*But how to find a flag? Found this command on Internet:*

```grep -oi '\S*flag\S*' /home/topson/workflows/xft/eBQRhHvx```

![image](https://github.com/user-attachments/assets/740c47c2-6a14-4c9f-9caa-9a179834b094)

### Task 3

Hypothetically, you find yourself in a directory with many files and want to move all these files to the directory of /home/francis/logs. What is the correct command to do this?
+ `mv * /home/francis/logs`

Hypothetically, you want to transfer a file from your /home/james/Desktop/ with the name script.py to the remote machine (192.168.10.5) directory of /home/john/scripts using the username of john. What would be the full command to do this?
+ `scp /home/james/Desktop/script.py john@192.168.10.5:/home/john/scripts`

How would you rename a folder named -logs to -newlogs
+ `mv -- -logs -newlogs`

How would you copy the file named encryption keys to the directory of /home/john/logs
+ `cp "encryption keys" /home/john/logs`

Find a file named readME_hint.txt inside topson's directory and read it. Using the instructions it gives you, get the second flag.
```
find /home/topson -type f -name readME_hint.txt 2>/dev/null
cat /home/topson/corperateFiles/RecordsFinances/readME_hint.txt
ls /home/topson/corperateFiles/RecordsFinances/
cd /home/topson/corperateFiles/RecordsFinances/
mv -- -MoveMe.txt '-march folder'
ls -- '-march folder'
```
![image](https://github.com/user-attachments/assets/473f5aaa-ebb8-4b67-a3a9-73dc55ab01fb)
```
cd -- '-march folder'
ls
./-runME.sh
```
![image](https://github.com/user-attachments/assets/04af778d-4311-4313-b0e6-1861cb22a72d)

*Well, that error made me thinking, what if I'll try execute bash script from another directory which contains -MoveMe.txt file.*

![image](https://github.com/user-attachments/assets/2b63aa96-28d3-41f7-bd1b-b889d17615b6)

*Yep. Good to know.*

### Task 4

Download the hash file attached to this task and attempt to crack the MD5 hash. What is the password?
```
john --format=Raw-MD5 --wordlist=/usr/share/wordlists/rockyou.txt 'hash1_1601657952696.txt'
```
+ secret123

![image](https://github.com/user-attachments/assets/139e37b7-5782-41f9-beba-24560533f28c)

SSH as sarah using: sarah@[10.10.200.9] and use the password: rainbowtree1230x
What is the hash type stored in the file hashA.txt

```
ssh sarah@10.10.200.9
find / -type f -name hashA.txt 2>/dev/null
cp sarah@10.10.200.9:"/home/sarah/system AB/server_mail/server settings/hashA.txt" hashA.txt
hash-identifier
```

+ MD4

![image](https://github.com/user-attachments/assets/450b73b7-b3e7-47e5-81e3-856a6da7ad16)

Crack hashA.txt using john the ripper, what is the password?

`john --format=Raw-MD4 --wordlist=/usr/share/wordlists/rockyou.txt hashA.txt `
+ admin

![image](https://github.com/user-attachments/assets/73688cb9-9f97-4b9c-ba60-6334622a39f5)

