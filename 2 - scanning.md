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

## protocolos de comunicação UDP e TCP
---
### UDP (User Datagram Protocol)
---
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
---
### TCP (Transmission Control Protocol)
---
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


