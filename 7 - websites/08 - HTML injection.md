# HTML Injection

Adicionalmente a injeçao de código JavaScript (XSS Injection), também pode ser possível a injeção de códigos HTML. 

HTML Injection é quando a aplicação insere conteúdo fornecido pelo usuário dentro do HTML da página **sem fazer a devida codificação/sanitização**, permitindo que esse usuário faça “entrar” novas tags e atributos HTML no documento.
Ex.: a página imprime `Olá, {nome}` e o campo **nome** recebe `<h1>Promoção falsa</h1>`, que passa a ser renderizado como parte da página.

Importante: **HTML Injection ≠ XSS.**

* Se a injeção **não** permite executar JavaScript (por ex., `<script>` ou `onerror=` são bloqueados), é “só” HTML Injection.
* Se for possível executar JS, então o problema já é **XSS**.

**Riscos principais**

1. **Falsificação de conteúdo (content spoofing) e defacement**
   – Inserir títulos, textos, imagens e avisos falsos, arranhando a reputação do site.
2. **Phishing dentro do próprio site**
   – Injetar **forms** ou botões visualmente legítimos para coletar logins, cartões, ou dados pessoais (ex.: “Refaça o login”).
3. **Sequestro de fluxo/engano do usuário (UI redressing)**
   – Criar elementos que desviam cliques (“Baixar nota fiscal” → link do invasor), alterar CTAs, e confundir navegação.
4. **Acionamento de requisições indesejadas (pode facilitar CSRF)**
   – Tags passivas como `<img>`, `<iframe>` e `<link>` podem disparar **GETs** para endpoints frágeis (ex.: roteadores/serviços internos), ou preparar um POST malicioso que o usuário acaba enviando.
5. **Quebra de layout, DoS visual, e problemas de SEO/acessibilidade**
   – Injeção de blocos enormes, iframes, fontes externas, ou meta-tags que bagunçam a página, pioram performance e ranking.
6. **Escalada para XSS (quando mal filtrado)**
   – Se o filtro falhar e aceitar `<script>` ou atributos de evento (`onclick`, `onerror`), o ataque vira **XSS**, abrindo espaço para roubo de sessão, keylogging, etc.

## HTML Injection vs XSS (resumo)

| Aspecto                 | HTML Injection                               | XSS                                                       |
| ----------------------- | -------------------------------------------- | --------------------------------------------------------- |
| Executa JavaScript?     | Não (em teoria)                              | Sim                                                       |
| O que o atacante altera | Estrutura/visual da página                   | Código e lógica no navegador                              |
| Principais riscos       | Phishing, spoofing, CSRF por GET, defacement | Roubo de sessão/dados, keylogging, pivot para outras APIs |
| Gravidade típica        | Média (pode ser alta)                        | Alta                                                      |

