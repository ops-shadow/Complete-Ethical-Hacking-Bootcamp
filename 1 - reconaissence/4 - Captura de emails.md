# Captura de email

## theHarvester
`# theHarvester -d {domínio} -b {bing, google..., all} -l {limite do número de resultados de pesquisa - default=500}

Nem sempre irá funcionar. Em caso de falha, tente novamente ou use outras ferramentas.

Entrega: 
- ASNS,
- Urls de interesse,
- LinkedIn users e LinkedIn Links,
- IPs,
- emails,
- pessoas,
- hosts

## Programas Python

### EmailHarvester
`$ python EmailHarvester.py -d {domínio} -e {máquina de pesquisa}`

options:
```
  -h, --help            show this help message and exit
  -d, --domain DOMAIN   Domain to search.
  -s, --save FILE       Save the results into a TXT and XML file (both).
  -e, --engine ENGINE   Select search engine plugin(eg. '-e google').
  -l, --limit LIMIT     Limit the number of results.
  -u, --user-agent USER-AGENT
                        Set the User-Agent request header.
  -x, --proxy PROXY     Setup proxy server (eg. '-x http://127.0.0.1:8080')
  --noprint             EmailHarvester will print discovered emails to terminal. It is possible to tell EmailHarvester not to print results to terminal with this option.
  -r, --exclude EXCLUDED_PLUGINS
                        Plugins to exclude when you choose 'all' for search engine (eg. '-r google,twitter')
  -p, --list-plugins    List all available plugins.
```

### email-scarper 
`$ python email-scarper.py                                                                    
[+] Enter Target URL To Scan: https://{domínio}`

## Spidefoot

`$ spiderfoot -l 127.0.0.1:5001`

Inicia o servidor local na porta 5001.<br>
Abra no navegador e acesse:

`http://127.0.0.1:5001`

Traz diversas informações como host, etc.

## Recon-ng 

O **Recon-ng** é um framework de OSINT em modo CLI (parecido com o Metasploit) e possui módulos para coletar e-mails de várias fontes.

**Passo 1 — Iniciar o Recon-ng**
`recon-ng`

**Passo 2 — Criar workspace**
`[recon-ng][default] > workspaces create teste`

**Passo 3 — Adicionar o domínio**
`[recon-ng][teste] > add domains exemplo.com`

**Passo 4 — Instalar módulos para buscar e-mails**
Por exemplo, módulo que coleta e-mails de WHOIS:<br>
`[recon-ng][teste] > marketplace install recon/domains-contacts/whois_pocs`<br>
E módulos de buscadores:
```
[recon-ng][teste] > marketplace install recon/domains-contacts/bing_domain_web
[recon-ng][teste] > marketplace install recon/domains-contacts/google_site_web
```
**Passo 5 — Executar os módulos**
```
[recon-ng][teste] > modules load recon/domains-contacts/whois_pocs
[recon-ng][teste][whois_pocs] > run
```
Depois faça o mesmo para os outros módulos que instalou.

**Passo 6 — Ver os e-mails encontrados**
`[recon-ng][teste] > show contacts`
<br>Isso lista todos os e-mails coletados até o momento.

## Matelgo

Ferramenta de OSINT (Open Source Intelligence) e link analysis usada para coletar, cruzar e visualizar informações públicas sobre pessoas, empresas, domínios, endereços de e-mail, redes sociais, infraestrutura de rede e muito mais.

1. Vá em File → New.
2. Escolha o tipo de grafo Blank Graph.
3. No canto esquerdo, na Entity Palette, procure por Domain. Arraste a entidade Domain para o grafo.
4. Clique duas vezes no nome e altere para o domínio alvo.
5. Com o domínio selecionado, clique com botão direito → Run Transform.
6. Escolha All Transforms ou procure por transform específicos: To Email Address [Using Search Engine], To Email Address [Common], To Email Addresses from WHOIS, To Email Addresses from Domain
7. Cada e-mail encontrado aparecerá como um novo nó no grafo. Você pode clicar nesses e-mails e rodar novos transforms para tentar:
- Descobrir perfis sociais.
- Encontrar outros domínios usados pelo mesmo e-mail.
- Verificar se aparecem em vazamentos de dados (HaveIBeenPwned, se o módulo estiver habilitado).
