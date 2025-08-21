# Netcat

O termo **Netcat** (ou abreviado como `nc`), é uma ferramenta de linha de comando bastante versátil para trabalhar com redes no Linux (e em outros sistemas). Pois permite criar conexões TCP e UDP, atuar como cliente ou servidor, transferir arquivos, testar portas, fazer debug de serviços de rede, entre outras coisas.

## O que é o Netcat:

* Programa que lê e escreve dados em conexões de rede usando **TCP** ou **UDP**.
* Pode ser usado tanto para **testar conexões** quanto para **criar túneis e backdoors** (em testes de segurança/pentests).
## Como usar – Exemplos práticos

### 1. Testar se uma porta está aberta

```bash
nc -zv 192.168.0.10 22
```

* `-z` = apenas verificar, sem enviar dados.
* `-v` = modo verboso.
  👉 Mostra se a porta 22 (SSH) do host está aberta.

### 2. Conectar-se a um serviço (cliente TCP simples)

```bash
nc example.com 80
```
Depois, você pode digitar manualmente uma requisição HTTP:

```
GET / HTTP/1.1
Host: example.com
```
### 3. Criar um servidor simples (escuta em porta)

```bash
nc -l -p 4444
```
* Espera conexões na porta 4444.
* Tudo que chegar pela rede será mostrado no terminal.

### 4. Transferência de arquivos entre duas máquinas

**Na máquina que vai receber:**

```bash
nc -l -p 1234 > arquivo_recebido.txt
```

**Na máquina que envia:**

```bash
nc destino 1234 < arquivo_origem.txt
```

### 5. Chat entre dois PCs

No PC1:

```bash
nc -l -p 5000
```

No PC2:

```bash
nc ip_do_pc1 5000
```
### 6. Port scanning básico

```bash
nc -zv 192.168.0.1 20-1000
```

👉 Escaneia portas de 20 a 1000 do host.

## Guia básico do netcat

| Ação                                  | Comando                                           | Explicação                                        |
| ------------------------------------- | ------------------------------------------------- | ------------------------------------------------- |
| **Conectar a uma porta (cliente)**    | `nc host porta`                                   | Abre conexão TCP simples.                         |
| **Servidor TCP (escuta)**             | `nc -l -p 4444`                                   | Escuta na porta 4444 e mostra dados recebidos.    |
| **Servidor UDP**                      | `nc -u -l -p 4444`                                | Escuta em UDP na porta 4444.                      |
| **Verificar porta aberta**            | `nc -zv host 22`                                  | Testa se a porta 22 (SSH) está aberta.            |
| **Escanear portas**                   | `nc -zv host 20-1000`                             | Escaneia portas de 20 a 1000.                     |
| **Enviar arquivo**                    | `nc destino 1234 < arquivo.txt`                   | Envia arquivo pela rede.                          |
| **Receber arquivo**                   | `nc -l -p 1234 > recebido.txt`                    | Escuta e salva o arquivo.                         |
| **Chat simples (máquina A)**          | `nc -l -p 5000`                                   | A escuta na porta 5000.                           |
| **Chat simples (máquina B)**          | `nc ip_A 5000`                                    | B conecta e troca mensagens.                      |
| **Banner grabbing (HTTP, SMTP etc.)** | `nc host 80` <br> depois digitar `GET / HTTP/1.1` | Identifica serviços e versões.                    |
| **Executar comando remoto**           | `nc -l -p 4444 -e /bin/bash`                      | Cria shell remoto (⚠️ perigoso, use só em labs!). |
| **Criar túnel inverso**               | `nc -l -p 5555 \| nc alvo 22`                     | Encaminha tráfego de uma porta para outra.        |

**Dicas rápidas**
- -v → modo verboso (mais detalhes).
- -n → não resolver DNS (mais rápido).
- -u → usar UDP (padrão é TCP).
- -z → só checar portas, sem enviar dados.
