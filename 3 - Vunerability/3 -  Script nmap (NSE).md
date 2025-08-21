# Usando Script Nmap (NSE)

- Misto de scan com vunerabilidade
- O sripts estão localizado na pasta `usr/share/nmap/scripts`

As pesquisas podem ser realizadas de duas formas:
- Por grupo de script
- Por script específico

## Grupos de Scripts

`$ namp --script {grupo de script} {alvo}`

**Exemplo:** vunerabilidades por grupo autorização
```
nmap --script auth 192.168.1.33                                                              
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-19 19:26 -03
Nmap scan report for 192.168.1.33
Host is up (0.00031s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp   open  ssh
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
| ssh-publickey-acceptance: 
|_  Accepted Public Keys: No public keys accepted
23/tcp   open  telnet
...
8180/tcp open  unknown
| http-default-accounts: 
|   [Apache Tomcat] at /manager/html/
|     tomcat:tomcat
|   [Apache Tomcat Host Manager] at /host-manager/html/
|_    tomcat:tomcat
MAC Address: 08:00:27:C7:CF:C6 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Host script results:
| smb-enum-users: 
|_  Domain: METASPLOITABLE; Users: backup, bin, bind, daemon, dhcp, distccd, ftp, games, gnats, irc, klog, libuuid, list, lp, mail, man, msfadmin, mysql, news, nobody, postfix, postgres, proftpd, proxy, root, service, sshd, sync, sys, syslog, telnetd, tomcat55, user, uucp, www-data

Post-scan script results:
| creds-summary: 
|   192.168.1.33: 
|     8180/nil: 
|       tomcat:tomcat - Valid credentials
|_      tomcat:tomcat - Valid credentials
Nmap done: 1 IP address (1 host up) scanned in 30.76 seconds
```
Considerando a primeira vunerabilidade encontrada:

`21/tcp   open  ftp
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)`

Podemos facilmente nos ogar no alvo através da porta de ftp com o user Anonymous, senha qualquer.
```
$ ftp 192.168.1.33 21
Connected to 192.168.1.33.
220 (vsFTPd 2.3.4)
Name (192.168.1.33:OpsShadow): Anonymous 
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
```

Para conhecer as categorias de scripts, [visite o website do nmap](https://nmap.org/book/nse-usage.html#nse-categories)

## Script específico

Os scripts do namap estão localizdos no diretório `/usr/share/nmap/scripts/`. 

**Exemplo:**

Se quiser saber os sripts sobre os serviços ftp.
```
$ ls /usr/share/nmap/scripts/ | grep ftp
ftp-anon.nse
ftp-bounce.nse
ftp-brute.nse
ftp-libopie.nse
ftp-proftpd-backdoor.nse
ftp-syst.nse
ftp-vsftpd-backdoor.nse
ftp-vuln-cve2010-4221.nse
tftp-enum.nse
tftp-version.nse
```
**Para detlhes do script:**

`$ nmap --script-help {nome do script}`

*Exemplo:*
```
$ nmap --script-h ftp-anon.nse
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-19 20:13 -03

ftp-anon
Categories: default auth safe
https://nmap.org/nsedoc/scripts/ftp-anon.html
  Checks if an FTP server allows anonymous logins.

  If anonymous is allowed, gets a directory listing of the root directory
  and highlights writeable files.
```
**Para executar o script:**

`$ nmap --script {nome do script} {alvo}`

*Exemplo:*
```
$ nmap --script ftp-anon.nse 192.168.1.33
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-19 20:18 -03
Nmap scan report for 192.168.1.33
Host is up (0.00063s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp   open  ssh
...
8180/tcp open  unknown
MAC Address: 08:00:27:C7:CF:C6 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 0.40 seconds
```
