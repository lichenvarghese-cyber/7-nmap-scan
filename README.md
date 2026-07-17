Metasploitable2 — Full Port Nmap Scan
Overview
This repo documents a full TCP port scan against a Metasploitable2 virtual machine, run in an isolated lab environment for security-testing practice. Metasploitable2 is an intentionally vulnerable Linux VM published by Rapid7 for training purposes only.
> ⚠️ **Scope note:** This scan was performed against a VM I own/control, on an isolated VirtualBox NAT/host-only network. Never scan systems you don't have explicit permission to test.
Lab Setup
Item	Value
Hypervisor	VirtualBox
Target VM	Metasploitable2
Target IP	`10.0.2.3`
Network mode	VirtualBox NAT
Scanner	Nmap 7.99
Date scanned	2026-07-17
Scan duration	161.17 seconds
Command Used
```bash
nmap --privileged -p- -sV -oN metasploitable_scan.txt 10.0.2.3
```
`-p-` — scan all 65535 TCP ports
`-sV` — detect service/version info
`-oN` — save output in normal format
Scan Results
```
# Nmap 7.99 scan initiated Fri Jul 17 07:14:21 2026 as: /usr/lib/nmap/nmap --privileged -p- -sV -oN metasploitable_scan.txt 10.0.2.3
Nmap scan report for 10.0.2.3
Host is up (0.00091s latency).
Not shown: 65505 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
21/tcp    open  ftp         vsftpd 2.3.4
22/tcp    open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp    open  telnet      Linux telnetd
25/tcp    open  smtp        Postfix smtpd
53/tcp    open  domain      ISC BIND 9.4.2
80/tcp    open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp   open  rpcbind     2 (RPC #100000)
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp   open  exec        netkit-rsh rexecd
513/tcp   open  login
514/tcp   open  tcpwrapped
1099/tcp  open  java-rmi    GNU Classpath grmiregistry
1524/tcp  open  bindshell   Metasploitable root shell
2049/tcp  open  nfs         2-4 (RPC #100003)
2121/tcp  open  ftp         ProFTPD 1.3.1
3306/tcp  open  mysql       MySQL 5.0.51a-3ubuntu5
3632/tcp  open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
5432/tcp  open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp  open  vnc         VNC (protocol 3.3)
6000/tcp  open  X11         (access denied)
6667/tcp  open  irc         UnrealIRCd
6697/tcp  open  irc         UnrealIRCd
8009/tcp  open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp  open  http        Apache Tomcat/Coyote JSP engine 1.1
8787/tcp  open  drb         Ruby DRb RMI (Ruby 1.8; path /usr/lib/ruby/1.8/drb)
43565/tcp open  nlockmgr    1-4 (RPC #100021)
50017/tcp open  java-rmi    GNU Classpath grmiregistry
51447/tcp open  mountd      1-3 (RPC #100005)
53369/tcp open  status      1 (RPC #100024)
MAC Address: 08:00:27:6E:EF:71 (Oracle VirtualBox virtual NIC)
Service Info: Hosts: metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jul 17 07:17:02 2026 -- 1 IP address (1 host up) scanned in 161.17 seconds
```
Full raw output: `metasploitable_scan.txt`
Key Findings
29 open TCP ports were identified, several tied to services with well-documented, publicly-known vulnerabilities — this is expected and intentional, as Metasploitable2 is designed as a deliberately vulnerable target.
Port	Service	Notable Version	Why It Stands Out
21	FTP	vsftpd 2.3.4	This specific build has a widely-documented backdoor vulnerability
23	Telnet	Linux telnetd	Unencrypted remote login protocol
512–514	rsh/rlogin	netkit-rsh	Legacy remote shell services, no encryption
1099/50017	Java RMI	GNU Classpath grmiregistry	RMI registries can allow remote code execution if misconfigured
1524	bindshell	"Metasploitable root shell"	Nmap flags this port directly as a pre-existing root shell — a known intentional backdoor on this image
2121	FTP	ProFTPD 1.3.1	Older version with known public CVEs
3306	MySQL	5.0.51a	End-of-life database version
3632	distccd	distcc v1	Known remote-code-execution history when exposed to untrusted networks
5432	PostgreSQL	8.3.0–8.3.7	End-of-life database version
5900	VNC	protocol 3.3	Old VNC protocol version with weak auth history
6667/6697	IRC	UnrealIRCd	This IRC daemon version has a well-known historical trojan/backdoor incident
8009	AJP13	Apache JServ	AJP protocol has known request-smuggling issues (e.g. "Ghostcat"-class bugs) in some versions
8180	HTTP	Tomcat/Coyote	Older Tomcat often ships with default/weak manager credentials
8787	DRb	Ruby DRb (Ruby 1.8)	Distributed Ruby has no built-in auth by default — can allow remote code execution
Next Steps
[ ] Run targeted NSE vulnerability scripts: `nmap --script vuln -p <port> 10.0.2.3`
[ ] Cross-reference each service version against CVE databases
[ ] Practice exploitation of each service in Metasploit (lab only)
[ ] Document exploitation steps and remediation notes per service
Disclaimer
This documentation is for educational purposes in a personal, isolated lab environment (Metasploitable2 is intentionally vulnerable by design, distributed by Rapid7 for this purpose). Do not use these techniques against systems without explicit authorization.
