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
