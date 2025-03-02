# [TryHackMe] Linux Strength Training room

## Task 2
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

But how to find a flag? Found this command on Internet:

```grep -oi '\S*flag\S*' /home/topson/workflows/xft/eBQRhHvx```

![image](https://github.com/user-attachments/assets/740c47c2-6a14-4c9f-9caa-9a179834b094)

## Task 3
