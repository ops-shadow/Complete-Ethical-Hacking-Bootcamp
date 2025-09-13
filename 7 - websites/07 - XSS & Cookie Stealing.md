# XSS (Cross-Site Scripting)

É uma vulnerabilidade em que **entrada não confiável** é **injetada** numa página e o navegador da vítima **executa JavaScript** sob o **mesmo domínio** do site legítimo. Isso pode permitir ações em nome do usuário, roubo de dados exibidos na página, phishing in-page e, em alguns casos, uso/roubo de sessão.

**Tipos principais:**

* **Reflected (não persistente):** o payload vem da requisição (URL, POST, cabeçalho) e é refletido na resposta.
* **Stored (persistente):** o payload é **armazenado** no servidor (ex.: comentários) e afeta quem visualizar.
* **DOM-Based:** a vulnerabilidade está no **cliente**; o JS manipula o DOM com dados não confiáveis (ex.: `location.search`) e cria HTML/script inseguro.

**Comparativo dos tipos de XSS**

| Aspecto                  | Reflected XSS                                        | Stored XSS                                                                 | DOM-Based XSS                                                                                        |
| ------------------------ | ---------------------------------------------------- | -------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| Definição curta          | Entrada maliciosa é **refletida** na resposta        | Entrada maliciosa é **salva** e servida depois                             | **JS do front** transforma dados não confiáveis em HTML/JS                                           |
| Persistência             | Não                                                  | **Sim** (atinge vários usuários)                                           | Depende do DOM; geralmente não persistente                                                           |
| Vetor típico             | Link malicioso / requisição forjada                  | Campo de cadastro/comentário/perfil                                        | Leitura de `location`, `hash`, `storage`, mensagens `postMessage`                                    |
| Onde falha               | Renderização no **servidor** sem escape por contexto | Armazenamento/saída no **servidor** sem escape por contexto                | **Código do cliente** (ex.: `innerHTML`, `document.write`)                                           |
| Precisa clicar em link?  | Geralmente **sim**                                   | Não necessariamente                                                        | Às vezes (se origem for URL/hash)                                                                    |
| Atinge todos visitantes? | Normalmente não                                      | **Sim**, quem abrir a página afetada                                       | Tipicamente só quem aciona o fluxo vulnerável                                                        |
| Sinks perigosos          | Saída em HTML/atributos/JS embutido                  | Idem                                                                       | `innerHTML`, `outerHTML`, `insertAdjacentHTML`, `eval`, `new Function`                               |
| Impacto típico           | Execução de ações, exfiltração de dados da página    | **Amplificado** (muitos usuários)                                          | Igual aos demais, mas sem passar pelo servidor                                                       |
| Deteção (alto nível)     | Ver se parâmetros aparecem sem escape na resposta    | Postar conteúdo com caracteres especiais e observar renderização           | Inspecionar JS/fluxo do DOM; forçar valores em URL/hash e ver mutações inseguras                     |
| Mitigações-chave         | Escape **por contexto** + validação + CSP            | Escape **por contexto** + validação na **entrada e saída** + moderação/CSP | Evitar sinks inseguros; usar `textContent`; templates com auto-escape; sanitizadores confiáveis; CSP |

> **Observações importantes**
>
> * **CSP** com **nonce/`strict-dynamic`** reduz muito o impacto/viabilidade.
> * Cookies de sessão: use **`HttpOnly`** (impede leitura via JS), **`Secure`** + **HTTPS/HSTS**, **`SameSite`**. HttpOnly **não impede** que XSS faça ações em seu nome, mas bloqueia leitura direta do cookie.
> * “Sanitização” deve ser **por contexto** (HTML, atributo, URL, JS, CSS). Não confie em uma única função “genérica”.

**Variantes e termos relacionados**

* **Blind XSS:** forma de Stored que dispara em consoles/admins (ex.: painel de suporte).
* **mXSS (Mutation XSS):** o navegador/sanitizador “muta” o HTML e reintroduz perigo.
* **Self-XSS:** engenharia social para a própria vítima colar código no console (não é falha do site).
* **UXSS:** exploração de bug no navegador/extensão, menos comum, alto impacto.

## Relected XSS

Com o uso do DVWA do metasploit, exploraremos a vunerabidade em reflected XSS, começando pelo nível baixo de segurança.

### Nível baixo de segurança

A intenção da página é criar um texto de boa-vinda com o nome do usuário, como abaixo:

![Comando](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_01.png)

![Resposta](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_02.png)

Contudo, se passarmos um JavaScript... (`<script>alert("XSS found")</script>`)

![Comando](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_03.png)

Ele é executado.

![Resposta](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_04.png)

Desta forma, podemos observar que a página não filtra a injeção de JavaScripts

### Nível médio de segurança

Ao tentar passarmos o mesmo JavaScript... (`<script>alert("XSS found")</script>`)

![Comando](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_03.png)

Ele **não é executado**.

![Resposta](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_05.png)

A página está filtrando. Contudo se passarmos variações do comando como
* `<SCRIPT>alert("XSS found")</SCRIPT>` (em maiúculas), ou
  ![Comando](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_06.png)
* `<scr<script>ipt>alert("XSS found")</script>` (ao filtrar a palavra *<script>* do comando, o comando original *<script>alert("XSS found")</script>* é reestabelecido.
  ![Comando](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_08.png)

O JavaScript é executado

![Resposta](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_04.png)

## Roubo de Cookie

