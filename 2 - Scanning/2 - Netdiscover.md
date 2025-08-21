# NETDISCOVER - Hosts ativos na rede local

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

### Evadindo firewalls e IDS (Intrur Detection System) com o nmap

Firewalls podem ser configurados de várias formas, sendo, portanto, difícil saber qual técnica irá funcionar a priori.

A seguir algumas técnicas de evasão:

#### Opção de fragmentação de pacotes (-f)

Faz o “fragmentation of packets” (fragmentação de pacotes), ou seja: divide os pacotes enviados pelo Nmap em pedaços menores.

Objetivo: tentar enganar firewalls, IDS ou filtros de pacotes que analisam pacotes maiores de uma vez só.

👉 Exemplo: em vez de mandar um pacote TCP completo de 24 bytes, o Nmap manda 2 pacotes fragmentados de 12 bytes.

`$ nmap -f {alvo}`

A opção ---mtu permite definir manualmente o tamanho máximo de cada fragmento.

`$ nmap --mtu {bits do fragmento} {alvo}`

 #### Opção de decoy

Permite usar **decoys** (IP’s falsos) no scan. O Nmap envia pacotes como se viessem de **vários IPs diferentes** ao mesmo tempo, incluindo o **seu IP real**. Isso dificulta para o **IDS/IPS ou admin da rede** identificar qual é o IP real do atacante.

`$ nmap D RND:{n} {alvo}` - O Nmap cria **n IPs aleatórios falsos** como decoys.

`$ nmap -D {IP1,IP2,...,ME} {alvo}`- O alvo verá pacotes vindos desses IPs **e do seu IP real (ME)**.

Para esconder o IP real de ataque, omita o IP "ME".

**Observações:**
* Se os decoys não forem IPs válidos (ou não existirem na rota até o alvo), eles **não respondem** → o administrador atento ainda pode perceber qual é o IP verdadeiro (o único que responde ao scan).
* IDS modernos podem **detectar uso de decoys**.
* É útil em labs ou para análise de logs, mas não é invisível - mas não garante anonimato total.

### Tabela de opções de evasão:
| Opção                                 | Nome                         | O que faz                                                                          | Exemplo de uso                                  |
| ------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------- | ----------------------------------------------- |
| `-f`                                  | **Fragmentation**            | Fragmenta pacotes em pedaços menores para tentar passar despercebido.              | `nmap -sS -f 192.168.1.10`                      |
| `--mtu <n>`                           | **Custom MTU**               | Define manualmente o tamanho máximo de fragmento.                                  | `nmap -sS --mtu 16 192.168.1.10`                |
| `-D <decoy>`                          | **Decoys**                   | Envia pacotes com IPs falsos junto ao real → dificulta identificar o atacante.     | `nmap -D RND:5 192.168.1.10` (5 IPs aleatórios) |
| `-S <IP>`                             | **Spoofed Source IP**        | Finge ser outro IP como origem (precisa de roteamento especial). Usualmente usado -Pn -e, e às vezes -D, ou Sniffing na rede | `nmap -S 10.0.0.99 192.168.1.10`                |
| `-g <port>` ou `--source-port <port>` | **Source Port Manipulation** | Força uso de porta de origem específica (ex.: 53, 80) para tentar passar firewall. | `nmap -g 53 192.168.1.10`                       |
| `--data-length <n>`                   | **Extra Data**               | Adiciona bytes aleatórios no payload → dificulta detecção por padrão fixo.         | `nmap --data-length 50 192.168.1.10`            |
| `--ttl <n>`                           | **Time-To-Live**             | Define valor TTL → pode alterar roteamento ou confundir IDS.                       | `nmap --ttl 5 192.168.1.10`                     |
| `--randomize-hosts`                   | **Host Randomization**       | Aleatoriza a ordem de hosts escaneados (evita padrões).                            | `nmap --randomize-hosts 192.168.1.0/24`         |
| `--scan-delay <t>`                    | **Delay entre pacotes**      | Insere atraso entre pacotes para reduzir detecção.                                 | `nmap --scan-delay 1s 192.168.1.10`             |
| `--badsum`                            | **Bad Checksum**             | Envia pacotes com checksum inválido (host real descarta, mas IDS pode logar).      | `nmap --badsum 192.168.1.10`                    |

#### -Pn

Por padrão, o Nmap tenta descobrir se o host está ativo (alive) antes de escanear:

- ICMP Echo (ping),
- TCP ACK,
- TCP SYN em portas comuns, etc.

Se não houver resposta, ele considera o host “down” e não prossegue. Contudo, muitos firewalls bloqueiam ping/ICMP, e até SYN/ACK de sondagem → host fica invisível pro Nmap.

Assim o -Pn informa para o nmap considerar o host como ativo e escanear mesmo sem resposta ao ping inicial.

Quando usar:
- Quando o alvo bloqueia ICMP/ping.
- Em ambientes cloud, onde o ping quase sempre é filtrado.
- Em pentests, para garantir que você não deixe hosts de fora.

#### -e {interface}

Permite escolher qual interface de rede o Nmap deve usar para enviar pacotes. Útil quando você tem mais de uma interface:

👉 Sem isso, o Nmap escolhe automaticamente a interface que acha correta.

Quando usar:
- Em labs com múltiplas redes (ex.: VM com rede NAT + host-only).
- Em scans via VPN (garante que o tráfego passe pelo túnel certo).
- Em spoofing (-S) ou decoys (-D), para garantir a saída correta.

### Opção timing template (-T)

* Controla a **velocidade/agressividade** do scan.
* Define parâmetros como:
  * atraso entre pacotes,
  * número de retransmissões,
  * tempo de timeout.
* Serve para equilibrar entre **rapidez x discrição x confiabilidade**.

O Nmap aceita valores de **0 a 5** (quanto maior, mais rápido e agressivo):

| Opção | Nome                 | Características                                               | Quando usar                                   |
| ----- | -------------------- | ------------------------------------------------------------- | --------------------------------------------- |
| `-T0` | **Paranoid**         | Extremamente lento, manda 1 probe a cada 5 minutos.           | Evasão de IDS muito sensível.                 |
| `-T1` | **Sneaky**           | Muito lento, mas ainda utilizável (1 probe a cada \~15s).     | Evasão em redes monitoradas.                  |
| `-T2` | **Polite**           | Reduz a velocidade, menos impacto na rede.                    | Quando não pode causar sobrecarga.            |
| `-T3` | **Normal (default)** | Balanceado, sem configurações extras.                         | Usado por padrão (se você não passar `-T`).   |
| `-T4` | **Aggressive**       | Bem rápido, reduz timeouts, paraleliza conexões.              | Ótimo em redes rápidas/estáveis (mais usado). |
| `-T5` | **Insane**           | Ultra agressivo, pode perder pacotes, gerar falsos negativos. | Só em laboratórios ou redes locais.           |

**Boas práticas:**
* **Pentest em rede local (lab)** → `-T4` (rápido e eficiente).
* **Pentest em rede corporativa real** → `-T2` ou `-T3` (menos impacto, evita alarmes).
* **Evasão de IDS/IPS** → `-T0` ou `-T1` (mas pode levar horas/dias).










