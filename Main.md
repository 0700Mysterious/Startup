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

Whatever the output of these two commands for you, is what you will use
