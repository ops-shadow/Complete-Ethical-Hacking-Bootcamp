# Burp Suite

**Conjunto de ferramentas para testar a segurança de aplicações web**. Ele funciona como um *proxy de interceptação* entre seu navegador e o alvo, permitindo **capturar, inspecionar, modificar e repetir** requisições HTTP(S)/WebSocket. Há edições **Community** (grátis) e **Professional/DAST** (pagas). A Pro traz **Burp Scanner** (varredura automatizada), entre outros recursos.

## Componentes essenciais (quando usar cada um)

* **Proxy/Intercept & HTTP history** – captura o tráfego do navegador para você alterar requisições/respostas. Use para mapeamento e testes manuais iniciais. 
* **Repeater** – reenvia **manualmente** a mesma requisição, com variações; ideal para testar hipóteses (SQLi/XSS/SSRF, etc.). 
* **Intruder** – automatiza ataques personalizados (fuzzing, brute force de parâmetros, enumeração), inserindo *payloads* em posições marcadas; possui tipos como **Sniper**. 
* **Scanner** (Pro/DAST) – varredura DAST que rastreia e audita o alvo automaticamente. Não está na Community. 
* **Target/Scope & Site map** – define **o escopo** (hosts/URLs) para focar só no que você tem autorização. 
* **Extensions (BApp Store)** – amplia o Burp (ex.: Param Miner, Autorize, JWT Editor, Turbo Intruder). 
* **Collaborator (OAST)** – detecta vulnerabilidades “invisíveis” (SSRF, *blind* XXE, etc.) por canais fora-de-banda. 

## Começar em 10 passos (fluxo recomendado)

1. **Instale e abra** o Burp. Crie um *Temporary project*. (Guia “Getting started”). 
2. **Use o navegador embutido do Burp** (*Open browser* em *Proxy ▸ Intercept*). Ele já vem configurado e evita dor de cabeça com certificados. 
3. Se preferir **navegador externo**, aponte o proxy para `127.0.0.1:8080` e **instale o CA do Burp** como autoridade confiável para HTTPS. 
4. **Defina o escopo** (*Target ▸ Scope*). Isso filtra o que você vê e evita sair do que foi autorizado. 
5. **Mapeie** o app navegando normalmente. O *HTTP history* vai se povoando; adicione caminhos interessantes ao *Site map*. 
6. **Intercepte** uma requisição crítica (login, API) e **modifique** cabeçalhos, cookies e parâmetros para validar comportamentos. 
7. Envie requisições relevantes ao **Repeater** para testar hipóteses, variando inputs e observando respostas. 
8. Para automação, marque posições e rode o **Intruder** (ex.: *fuzz* de um parâmetro, enum de IDs). 
9. Se tiver **Pro**, rode um **scan** (crawl+audit) e revise as *issues* encontradas. 
10. **Itere**: refine escopo, ajuste *payloads*, instale extensões úteis e, quando cabível, use **Collaborator** para casos *blind*. 

## O que dá para “passar” numa resposta HTTP com o Burp (na prática)

* **Cabeçalhos** (Auth, Cookies, Origin, Content-Type, etc.) e **parâmetros** (query, corpo JSON/form, multipart).
* **Método** (GET/POST/PUT/PATCH/DELETE), **path** e **corpo** — tudo editável no Repeater/Intruder para verificar validações, controle de acesso, injeções e erros lógicos. 

## Dicas de produtividade

* **HTTP/2/3 vs 1.1**: no Repeater você pode controlar conexão e suporte HTTP/2 conforme necessário. 
* **Scope bem definido** = menos ruído e menos risco de tráfego fora de autorização. 
* **BApp Store**: instale extensões que economizam horas em testes de autorização e descoberta de parâmetros.

