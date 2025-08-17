# Scanning (Varredura / Enumeração Técnica)

## Objetivo:

Transformar as informações coletadas em dados técnicos concretos sobre vulnerabilidades potenciais.

- É uma fase mais ativa e intrusiva.
- Usa ferramentas automáticas e semiautomáticas para interagir com os sistemas.
- Exemplos de atividades:
  - Port scanning (Nmap) para identificar portas abertas e serviços rodando.
  - Service/version detection (ex.: qual versão do Apache, MySQL).
  - Enumeração de usuários, diretórios e compartilhamentos.
  - Detecção de vulnerabilidades conhecidas (ex.: Nessus, OpenVAS).

👉 Aqui o foco é identificar superfícies de ataque exploráveis.

Enquanto na fase  de **reconnaissance** queremos saber sobre o alvo com coleta de informações estratégicas e contextuais (o quê e quem), na fase de **scanning** queremos testar o alvo e descobrir vulnerabilidades técnicas a partir da interação direta (o como).

---
## protocolos de comunicação UDP e TCP

### UDP (User Datagram Protocol)

Protocolo da camada de Transporte do modelo TCP/IP (camada 4 do OSI). É considerado um protocolo de transporte não orientado à conexão.

#### Objetivo do UDP

Fornecer um meio rápido e simples de enviar dados entre aplicações na rede, sem precisar estabelecer ou manter uma conexão (diferente do TCP). É leve, porque não exige controle de conexão, verificação de entrega ou ordenação de pacotes.

Favorece velocidade e eficiência, em vez de confiabilidade.

#### Como funciona

1. Encapsulamento: A aplicação gera dados → são encapsulados em um datagrama UDP.

2. Cabeçalho simples: O cabeçalho tem apenas 4 campos (8 bytes):

  - Porta de origem
  - Porta de destino
  - Comprimento
  - Checksum (simples verificação de erros, opcional em IPv4)

3. Transmissão direta: O datagrama é entregue à camada de rede (IP) e enviado ao destino.

4. Sem garantias:
  - Não garante entrega.
  - Não garante ordem.
  - Não retransmite pacotes perdidos.

👉 Cada pacote é independente (stateless).

#### Quando é utilizado

O UDP é preferido quando a velocidade é mais importante que a confiabilidade absoluta:
- Streaming de áudio e vídeo (YouTube ao vivo, IPTV, Netflix em transmissões de baixa latência).
- Jogos online (FPS, MMO), onde perder um pacote é menos crítico que esperar retransmissão.
- DNS (Domain Name System) → consultas e respostas rápidas.
- VoIP (chamadas de voz via Internet).
- Protocolos de descoberta (DHCP, TFTP, SNMP).
- VPNs leves (ex.: WireGuard, OpenVPN em modo UDP).

### TCP (Transmission Control Protocol)

Protocolo da camada de **Transporte** (camada 4 do modelo TCP/IP, equivalente à camada de Transporte do OSI). Ele é **orientado à conexão** e **confiável**.

#### Objetivo do TCP

O TCP foi criado para permitir a **entrega confiável de dados entre duas aplicações** conectadas em rede, mesmo em ambientes instáveis como a Internet.

* Ele garante que os pacotes cheguem **na ordem correta** e **sem perdas**.
* Faz isso através de mecanismos de **controle de fluxo, confiabilidade e congestionamento**.

#### Como funciona

1. **Estabelecimento da conexão**

   * Usa o famoso **3-way handshake**:
     * SYN → SYN/ACK → ACK.
   * Isso garante que cliente e servidor estão prontos para trocar dados.

2. **Transmissão de dados confiável**

   * Os dados são divididos em **segmentos TCP**.
   * Cada segmento recebe um número de sequência (Sequence Number).
   * O receptor confirma a recepção com ACKs (Acknowledgements).

3. **Retransmissão de pacotes**

   * Se um ACK não chega, o TCP retransmite os dados.

4. **Controle de fluxo**

   * Usa a técnica de **janela deslizante (sliding window)** para evitar sobrecarregar o receptor.

5. **Encerramento da conexão**

   * Geralmente por **4-way handshake** (FIN → ACK → FIN → ACK).

#### Quando é utilizado

O TCP é usado quando a **confiabilidade** é mais importante que a **velocidade bruta**:
* HTTP/HTTPS (navegação web)
* E-mail (SMTP, IMAP, POP3)
* Transferência de arquivos (FTP, SFTP, SMB)
* Acesso remoto seguro (SSH, Telnet)
* Banco de dados em rede (MySQL, PostgreSQL, etc.)

### Comparação TCP x UDP

| Característica | TCP                                         | UDP                          |
| -------------- | ------------------------------------------- | ---------------------------- |
| Conexão        | Orientado à conexão (handshake)             | Não orientado à conexão      |
| Confiabilidade | Alta (ACKs, retransmissão, ordem garantida) | Baixa (sem garantias)        |
| Overhead       | Maior (cabeçalho de 20 bytes)               | Menor (cabeçalho de 8 bytes) |
| Velocidade     | Mais lento (controle pesado)                | Mais rápido (leve)           |
| Uso típico     | Web, e-mail, arquivos, SSH                  | Streaming, VoIP, DNS, jogos  |

---
## Scanning da rede

### NETDISCOVER - Descobrindo hosts ativos na rede local

O **`netdiscover`** é uma ferramenta bem simples e útil em **pentests** ou administração de rede.

* Serve para **descobrir hosts ativos na rede local** (LAN), mostrando seus **IPs, MAC addresses e fabricantes das placas de rede**.
* Muito usado em redes desconhecidas, especialmente quando não há servidor DHCP ou quando você não sabe quais dispositivos estão conectados.

Trabalha principalmente com **ARP (Address Resolution Protocol)**:

1. Envia requisições ARP na rede local (broadcast).
2. Dispositivos que responderem revelam seu **IP** e **MAC address**.
3. O `netdiscover` monta uma tabela de hosts ativos.

Pode operar em dois modos:

* **Passivo**: apenas escuta tráfego ARP da rede, sem enviar nada (mais furtivo).
* **Ativo**: envia pacotes ARP para descobrir hosts (mais rápido e completo).

*Exemplo de uso:*
```
$ netdiscover

 Currently scanning: 192.168.237.0/16   |   Screen View: Unique Hosts                            
                                                                                                 
 19 Captured ARP Req/Rep packets, from 5 hosts.   Total size: 924                                
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
 192.168.1.33    08:00:27:c7:cf:c6      1      42  PCS Systemtechnik GmbH                        
 192.168.1.3     08:6f:48:00:68:36      1      42  Shenzhen iComm Semiconductor CO.,LTD          
 192.168.1.29    5c:cd:5b:94:a0:d4      7     294  Intel Corporate                               
 192.168.1.26    5c:52:1e:9c:38:0a      1      42  Nintendo Co.,Ltd                              
 192.168.1.254   78:3e:a1:89:7d:10      9     504  Nokia Shanghai Bell Co., Ltd.                 
```

### NMAP

Ferramenta de **segurança e administração de redes**. Serve para:

* **Descobrir hosts** ativos em uma rede.
* **Escanear portas abertas** em um dispositivo.
* **Identificar serviços** rodando (ex.: Apache, MySQL, SSH).
* **Detectar versão de serviços** e até o **sistema operacional**.
* Automatizar auditorias de segurança.

👉 Em pentests, o Nmap é a base para o **Scanning**.

**Como funciona**

* Ele envia pacotes (TCP, UDP, ICMP, ARP, etc.) para um alvo.
* Analisa as respostas para determinar se uma porta está:

  * **open** (aberta),
  * **closed** (fechada),
  * **filtered** (filtrada por firewall).

#### Comando básico:
```
$ nmap {alvo}<br>
hostnames, IP addresses, networks, etc.
  Ex: scanme.nmap.org, microsoft.com/24, 192.168.0.1; 10.0.0-255.1-254
```
Esse comando escaneia o host com as opções padrão do Nmap.
* Escaneia as **1.000 portas TCP mais comuns**.
* Tenta descobrir quais estão abertas.

*Exemplo:*
```
$ nmap 192.168.1.33                                                                             
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-16 18:28 -03
Nmap scan report for 192.168.1.33
Host is up (0.00023s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
6667/tcp open  irc
8009/tcp open  ajp13
8180/tcp open  unknown
MAC Address: 08:00:27:C7:CF:C6 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 0.38 seconds
```

#### Outro comanandos utilizados: 

| Comando                         | Nome do Scan                    | O que faz                                                       | Quando usar                                              |
| ------------------------------- | ------------------------------- | --------------------------------------------------------------- | -------------------------------------------------------- |
| `nmap -sS <alvo>`               | **SYN Scan (Stealth Scan)**     | Envia apenas SYN (meio handshake) → mais rápido e furtivo       | Pentests, quando você não quer gerar muito log no alvo   |
| `nmap -sT <alvo>`               | **TCP Connect Scan**            | Faz o handshake completo TCP                                    | Quando não tem privilégios root (menos furtivo)          |
| `nmap -sU <alvo>`               | **UDP Scan**                    | Escaneia portas UDP (DNS, SNMP, DHCP, etc.)                     | Descobrir serviços que rodam em UDP                      |
| `nmap -sV <alvo>`               | **Version Detection**           | Detecta versão do serviço rodando em cada porta                 | Identificar vulnerabilidades específicas de versão       |
| `nmap -O <alvo>`                | **OS Detection**                | Tenta identificar o sistema operacional                         | Mapear alvo (ex.: Linux, Windows, etc.)                  |
| `nmap -A <alvo>`                | **Aggressive Scan**             | Combina `-sV` + `-O` + traceroute + scripts básicos             | Recon rápido e detalhado (gera muito log)                |
| `nmap -p <porta> <alvo>`        | **Port Scan Específico**        | Escaneia apenas a(s) porta(s) indicada(s)                       | Focado em uma porta (ex.: 22, 80, 443)                   |
| `nmap -p- <alvo>`               | **Full Port Scan**              | Escaneia todas as **65.535 portas TCP**                         | Quando quer ter certeza absoluta de tudo que está aberto |
| `nmap -sC <alvo>`               | **Script Scan (NSE default)**   | Executa scripts de enumeração padrão do Nmap                    | Coletar infos extras automaticamente                     |
| `nmap --script <script> <alvo>` | **Nmap Scripting Engine (NSE)** | Executa scripts específicos (ex.: vulnerabilidade, brute force) | Pentests avançados ou testes direcionados                |










