# HTML Injection

Adicionalmente a injeçao de código JavaScript (XSS Injection), também pode ser possível a injeção de códigos HTML. 

HTML Injection é quando a aplicação insere conteúdo fornecido pelo usuário dentro do HTML da página **sem fazer a devida codificação/sanitização**, permitindo que esse usuário faça “entrar” novas tags e atributos HTML no documento.
Ex.: a página imprime `Olá, {nome}` e o campo **nome** recebe `<h1>Promoção falsa</h1>`, que passa a ser renderizado como parte da página.

Importante: **HTML Injection ≠ XSS.**

* Se a injeção **não** permite executar JavaScript (por ex., `<script>` ou `onerror=` são bloqueados), é “só” HTML Injection.
* Se for possível executar JS, então o problema já é **XSS**.

**Riscos principais:**

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

## Laboratório

### Usando tag de formatação

Usando o DVWA, a mesma página para *XSS Stored*, tentaremos inserir um código simples '<h1>Vulnerável</h1> no campo comentário.

![Comando](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/24e40b509450a8b7eb59f94de65a6b81c13a3850/7%20-%20websites/img/html_01.png)

Obtendo:

![Resultado](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/24e40b509450a8b7eb59f94de65a6b81c13a3850/7%20-%20websites/img/html_02.png)

Observamos que a página aceitou a injeção do tag.

### Seqquetrando a página

Agora tentaremos usar um comando malicioso, com o objetivo de sequetrar a página redirecionando-a para outro website. Para isso utilizaremos o camando: `<meta http-equiv="refresh" content="0; url=https://ofuxico.com.br/">`.

*Exlicando o código:*
* *<meta>: Tag usada para fornecer metadados sobre o documento HTML*.
*  *http-equiv="refresh": Simula um cabeçalho HTTP que diz ao navegador para "atualizar" ou "redirecionar"*.
*  *content="0; url=...":*
   *  *O número 0 indica o tempo de espera em segundos antes do redirecionamento*.
   *  *O url define o destino da nova página*.

![Comando](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/24e40b509450a8b7eb59f94de65a6b81c13a3850/7%20-%20websites/img/html_03.png)

Observamos que o campo de comentário não é grande o suficiente para inserir o comando. Desta forma, ao inspecionar a página alteramos o maxlenght do campo para um valor maior.

![Maxlenght](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/24e40b509450a8b7eb59f94de65a6b81c13a3850/7%20-%20websites/img/html_04.png)

Agora, inserindo o código HTML...

![Código](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/24e40b509450a8b7eb59f94de65a6b81c13a3850/7%20-%20websites/img/html_05.png)

Resultado: todos os que abrirem a página serão redirecionados automaticamente para outro website. A página foi "sequestrada".

![Resutado](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/24e40b509450a8b7eb59f94de65a6b81c13a3850/7%20-%20websites/img/html_06.png)
