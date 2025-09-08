# Coleta de Informação

Além das práticas descritas na fase de reconaissence, podemos utilizar outras ferramentass que nos auxiliarão na enumeração dos diretórios e arquivos de um website.

## 1. Dirb

O DIRB é uma ferramenta clássica de pentest incluída no Kali Linux, usada para descobrir diretórios e arquivos ocultos em servidores web. Ele funciona como um scanner de conteúdo web, lançando ataques baseados em dicionário para tentar acessar URLs que não estão visíveis no site — como /admin, /login, /backup.zip, etc.

Como o DIRB funciona

Ele pega uma lista de palavras (wordlist) e tenta combiná-las com a URL base, verificando se o servidor responde com sucesso. 

Exemplo:
```
dirb http://alvo.com
```
O Dirb vai testar URLs como:
- `http://alvo.com/admin`
- `http://alvo.com/index.php`
- `http://alvo.com/config/`

E te mostrar quais retornam códigos HTTP válidos (200, 403, etc).

*Observação: o Dirb permite a utilização de wordlist personalizada.*

Dicas avançadas

- Combine com **proxy** (como Burp Suite) para interceptar requisições.
- Use wordlists maiores como as do **SecLists** para resultados mais profundos.
- Pode ser usado como scanner de CGI, mas não detecta vulnerabilidades — apenas conteúdo acessível.

## 2. HTTrack – Navegador offline

Recursos:
* Copia HTML, imagens, scripts e estrutura de links.
* Pode atualizar sites já baixados.
* Muito útil para análise forense ou backup de conteúdo público.

Documentação oficial do [HTTrack](https://www.kali.org/tools/httrack/) 

## 3. SEToolkit (Social Engineering Toolkit) – Clonagem para testes de phishing

Ferramenta voltada para simulações de ataques de engenharia social, como clonagem de páginas de login.

Execução:

`sudo setoolkit`

Passos principais:

Escolha a opção 1 – Social-Engineering Attacks
Depois 2 – Website Attack Vectors
Em seguida 3 – Credential Harvester Attack Method
E então 2 – Site Cloner
Insira a URL do site que deseja clonar e o IP do seu servidor

Resultado:

O SEToolkit cria uma cópia funcional da página e escuta por credenciais inseridas — somente para fins éticos e educativos, claro.

[Exemplo de uso do SEToolKit](https://github.com/AlciJunior/desafio-phishing-cybersecurity)

---

## 🧭 **Algumas Ferramentas para Enumeração de Websites**

| Ferramenta        | Finalidade Principal                                      | Observações |
|-------------------|-----------------------------------------------------------|-------------|
| **Nmap**          | Varredura de portas, serviços e sistema operacional       | Pode detectar servidores web e versões de software |
| **WhatWeb**       | Identifica tecnologias usadas no site (CMS, frameworks)   | Muito útil para saber se o site usa WordPress, Apache, etc. |
| **Wappalyzer**    | Similar ao WhatWeb, mas com foco em extensões de navegador| Também disponível como CLI |
| **Dirb / Gobuster** | Enumeração de diretórios e arquivos ocultos             | Revela páginas não listadas, como `/admin` ou `/backup` |
| **Nikto**         | Scanner de vulnerabilidades em servidores web             | Detecta configurações inseguras e falhas conhecidas |
| **WPScan**        | Focado em WordPress: plugins, temas, usuários             | Excelente para sites WordPress |
| **Burp Suite**    | Interceptação e análise de tráfego HTTP/S                 | Ideal para fuzzing, manipulação de parâmetros e enumeração manual |
| **Metasploit**    | Framework para exploração e enumeração avançada           | Pode integrar com Nmap e outros scanners |
| **SSLScan / sslyze** | Verifica configurações de SSL/TLS                      | Útil para identificar protocolos inseguros |
| **theHarvester**  | Coleta e-mails, domínios e subdomínios via OSINT          | Usa fontes como Google, Bing, Shodan |
| **Sublist3r**     | Enumeração de subdomínios                                 | Pode revelar painéis de admin ou APIs escondidas |
