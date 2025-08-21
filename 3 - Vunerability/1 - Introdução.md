# pesquisa de vulnerabilidades

Momento em que, após o mapeamento e a varredura do alvo, o pentester passa a identificar **quais falhas de segurança conhecidas** podem estar presentes nos sistemas analisados.

### Objetivo:

Transformar os dados brutos coletados (IPs, portas abertas, serviços, versões de software, frameworks, etc.) em **possíveis pontos fracos exploráveis**, comparando-os com bancos de dados de vulnerabilidades conhecidas.

### Principais atividades

1. **Identificação de versões de software e serviços**
   – A partir do fingerprinting feito na fase de varredura, verifica-se qual versão de servidor web, banco de dados, sistema operacional ou aplicação está em uso.

2. **Correlação com vulnerabilidades conhecidas**
   – Consulta a bancos de dados como:

   * CVE (Common Vulnerabilities and Exposures)
   * NVD (National Vulnerability Database)
   * Exploit-DB
   * OWASP para falhas web comuns (SQLi, XSS, CSRF, etc.)

3. **Uso de scanners de vulnerabilidade**
   – Ferramentas como **Nessus, OpenVAS, Nikto, Nmap NSE scripts, Burp Suite, OWASP ZAP**, que ajudam a automatizar a detecção de falhas.

4. **Análise manual e validação**
   – O pentester não depende só de ferramentas: faz também testes manuais para confirmar resultados (ex.: inputs maliciosos em formulários, análise de headers, configuração incorreta de TLS/SSL).

### Resultado esperado

No fim dessa fase, o pentester terá:

* Uma lista priorizada de vulnerabilidades prováveis.
* Informações técnicas sobre cada vulnerabilidade (criticidade, impacto e exploitabilidade).
* Base para avançar à fase seguinte: **exploração**.

## Pesquisa manual de vunerabilidade

### Pesquisa em portais como Google, Bing, ou Yahoo


Prompt de pesquisa: `{versão tecnologia} sploit`

**Exemplo:** Sabendo que o IP 192.168.1.33 tem a porta 21 aberta (ftp), rodando vsftpd 2.3.4, podemos pesquisar no Google:

`vsftpd 2.3.4 sploit`

### Pesquisa no Kali Linux

Para pesquisar vunerabilidades na base de dados do Kali:

`$ searchsploit {versão tecnologia}`

Para descobrir o arquivo com o exploit:

`$ locate {sploit}`

**Exemplo:** 
```
$ searchsploit 'Apache Tomcat 1.1'                                                   
---------------------------------------------------------------- ---------------------------------
 Exploit Title                                                  |  Path
---------------------------------------------------------------- ---------------------------------
Apache Tomcat < 5.5.17 - Remote Directory Listing               | multiple/remote/2061.txt
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal             | unix/remote/14489.c
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal (PoC)       | multiple/remote/6229.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 -  | jsp/webapps/42966.py
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 -  | windows/webapps/42953.txt
---------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results

$ locate jsp/webapps/42966.py                                                                   
/usr/share/exploitdb/exploits/jsp/webapps/42966.py
```
## Usando Script Nmap (NSE)

- Misto de scan com vunerabilidade
- O sripts estão localizado na pasta `usr/share/nmap/scripts`

As pesquisas podem ser realizadas de duas formas:
- Por grupo de script
- Por script específico

### Grupos de Scripts

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

### Script específico

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
## Utiizando o Nessus

O Nessus é uma ferramanta paga com alto custo. Contudo a Tenable disponibiliza uma versão gratuita - Nessus Essential, limitada a 16 IPs.

A alternativa gratuita ao Nessus é o OpenVAS/GVM, mas é mais complexo o seu uso.

**Inciando o Nessus:**
- No terminal: `$ sudo systemctl start nessusd`
- No web browser: [`https://localhost:8834`](https://localhost:8834)

**Encerrando o Nessus:**
- No terminal: `$ sudo systemctl stop nessusd`

[**Tutorial rápido de uso do Nessus**](https://www.youtube.com/watch?v=5bK1HFL-u-g)

[**Outra opção**](https://www.youtube.com/watch?v=a1rcJFTrbRM)

## Utilizando o GVM

**Inciando o Nessus:**
- No terminal: `$ sudo gvm-start`
- Abrirá automaticamente o web browser: [`https://127.0.0.1:9392`](https://127.0.0.1:9392)

**Encerrando o GVM:**
- No terminal: `$ sudo gvm-stop`

[**Tutorial rápido de uso do GVM**](https://www.youtube.com/watch?v=LGh2SetiKaY)

[**Outra opção**](https://www.youtube.com/watch?v=PN5SPuSirm8)

