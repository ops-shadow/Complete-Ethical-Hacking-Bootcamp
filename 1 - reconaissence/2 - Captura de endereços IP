# Captura de endereços IP

## Comando ping
A forma mais simples é através do comando ping

`$ ping {domínio}`

*Exemplo:* 
```
$ ping google.com
PING google.com (142.250.219.142) 56(84) bytes of data.
64 bytes from tzrioa-aj-in-f14.1e100.net (142.250.219.142): icmp_seq=1 ttl=117 time=5.97 ms
64 bytes from tzrioa-aj-in-f14.1e100.net (142.250.219.142): icmp_seq=2 ttl=117 time=6.03 ms
64 bytes from tzrioa-aj-in-f14.1e100.net (142.250.219.142): icmp_seq=3 ttl=117 time=9.27 ms
^C
--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 5.967/7.088/9.271/1.543 ms
```
No exemplo o IP do Google é *142.250.219.142*.

## Comando nslookup
`$ nslookup {domínio}`

*Exemplo:*
```
$ nslookup google.com
Server:         192.168.1.254
Address:        192.168.1.254#53

Non-authoritative answer:
Name:   google.com
Address: 142.250.219.142
Name:   google.com
Address: 2800:3f0:4004:80f::200e
```
Além do endereço IP, o camando também retorna o endereço MAC

## Comando dig
`$ dig {domínio}`

*Exemplo:*
```
$ dig google.com

; <<>> DiG 9.20.11-4-Debian <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 46648
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             174     IN      A       142.250.219.142

;; Query time: 7 msec
;; SERVER: 192.168.1.254#53(192.168.1.254) (UDP)
;; WHEN: Fri Aug 15 18:53:20 -03 2025
;; MSG SIZE  rcvd: 55
```
## Comando whois
`$ whois {domínio}`

Fornece informações do website whois, incluindo informações de registro, DNS, etc.

## Outras opções

Websites como o whois.com, nslookup.io, iplocation.io, etc.

# Captura de tecnologias

### Comando whatweb
`$ whatweb {domínio} -v --log-verbose={arquivo}`

Busca informações de tecnologias utilizadas, como servidores, sistemas, cookies, etc..

*Exemplo:*
```
$ whatweb domai.com -v
                                                                        
WhatWeb report for http://domai.com
Status    : 301 Moved Permanently
Title     : <None>
IP        : 207.66.141.189
Country   : UNITED STATES, US

Summary   : RedirectLocation[https://www.domai.com/]

Detected Plugins:
[ RedirectLocation ]
        HTTP Server string location. used with http-status 301 and 
        302 

        String       : https://www.domai.com/ (from location)

HTTP Headers:
        HTTP/1.1 301 Moved Permanently
        Content-length: 0
        Location: https://www.domai.com/
        Connection: close

WhatWeb report for https://www.domai.com/
Status    : 200 OK
Title     : <None>
IP        : 207.66.141.189
Country   : UNITED STATES, US

Summary   : Bootstrap, Cookies[_csrf,msite_prod_GoId,site_test,site_test_order], Email[upgrades_hero_desktop@2x.jpg,upgrades_hero_phone@2x.jpg], HTML5, HTTPServer[nginx/1.15.6], HttpOnly[_csrf], nginx[1.15.6], Script[initialState], Strict-Transport-Security[max-age=15552000; includeSubDomains], UncommonHeaders[service-worker-allowed,content-security-policy,cross-origin-resource-policy,x-dns-prefetch-control,expect-ct,x-download-options,x-content-type-options,origin-agent-cluster,x-permitted-cross-domain-policies,referrer-policy,x-cache-status], X-Frame-Options[SAMEORIGIN], X-XSS-Protection[0]

Detected Plugins:
[ Bootstrap ]
        Bootstrap is an open source toolkit for developing with 
        HTML, CSS, and JS. 

        Website     : https://getbootstrap.com/

[ Cookies ]
        Display the names of cookies in the HTTP headers. The 
        values are not returned to save on space. 

        String       : _csrf
        String       : msite_prod_GoId
        String       : site_test
        String       : site_test_order

[ Email ]
        Extract email addresses. Find valid email address and 
        syntactically invalid email addresses from mailto: link 
        tags. We match syntactically invalid links containing 
        mailto: to catch anti-spam email addresses, eg. bob at 
        gmail.com. This uses the simplified email regular 
        expression from 
        http://www.regular-expressions.info/email.html for valid 
        email address matching. 

        String       : upgrades_hero_desktop@2x.jpg,upgrades_hero_phone@2x.jpg

[ HTML5 ]
        HTML version 5, detected by the doctype declaration 


[ HTTPServer ]
        HTTP server header string. This plugin also attempts to 
        identify the operating system from the server header. 

        String       : nginx/1.15.6 (from server string)

[ HttpOnly ]
        If the HttpOnly flag is included in the HTTP set-cookie 
        response header and the browser supports it then the cookie 
        cannot be accessed through client side script - More Info: 
        http://en.wikipedia.org/wiki/HTTP_cookie 

        String       : _csrf

[ Script ]
        This plugin detects instances of script HTML elements and 
        returns the script language/type. 

        String       : initialState

[ Strict-Transport-Security ]
        Strict-Transport-Security is an HTTP header that restricts 
        a web browser from accessing a website without the security 
        of the HTTPS protocol. 

        String       : max-age=15552000; includeSubDomains

[ UncommonHeaders ]
        Uncommon HTTP server headers. The blacklist includes all 
        the standard headers and many non standard but common ones. 
        Interesting but fairly common headers should have their own 
        plugins, eg. x-powered-by, server and x-aspnet-version. 
        Info about headers can be found at www.http-stats.com 

        String       : service-worker-allowed,content-security-policy,cross-origin-resource-policy,x-dns-prefetch-control,expect-ct,x-download-options,x-content-type-options,origin-agent-cluster,x-permitted-cross-domain-policies,referrer-policy,x-cache-status (from headers)                    

[ X-Frame-Options ]
        This plugin retrieves the X-Frame-Options value from the 
        HTTP header. - More Info: 
        http://msdn.microsoft.com/en-us/library/cc288472%28VS.85%29.
        aspx

        String       : SAMEORIGIN

[ X-XSS-Protection ]
        This plugin retrieves the X-XSS-Protection value from the 
        HTTP header. - More Info: 
        http://msdn.microsoft.com/en-us/library/cc288472%28VS.85%29.
        aspx

        String       : 0

[ nginx ]
        Nginx (Engine-X) is a free, open-source, high-performance 
        HTTP server and reverse proxy, as well as an IMAP/POP3 
        proxy server. 

        Version      : 1.15.6
        Website     : http://nginx.net/

HTTP Headers:
        HTTP/1.1 200 OK
        Server: nginx/1.15.6
        Date: Fri, 15 Aug 2025 22:06:31 GMT
        Content-Type: text/html; charset=utf-8
        Transfer-Encoding: chunked
        Connection: close
        Service-Worker-Allowed: /
        Set-Cookie: _csrf=tAnAtRNa8pZ-T60itACGpuo8; Path=/; HttpOnly; Secure
        Set-Cookie: msite_prod_GoId=7d2bcb8a-4820-45e2-9f2d-f720f3f803db; Domain=.undefined; Path=/
        Set-Cookie: site_test=; Domain=.domai.com; Path=/; Expires=Thu, 01 Jan 1970 00:00:00 GMT
        Set-Cookie: site_test_order=; Domain=.domai.com; Path=/; Expires=Thu, 01 Jan 1970 00:00:00 GMT
        Content-Security-Policy: default-src 'self' blob: *.cachefly.net *.b-cdn.net *.metartnetwork.com *.metart.com;connect-src 'self' blob: wss: *.cachefly.net *.b-cdn.net *.metartnetwork.com *.metart.com *.zdassets.com *.zendesk.com *.atlassian.com *.atl-paas.net *.metart.network *.google.com *.gstatic.com *.google-analytics.com *.googleapis.com *.doubleclick.net *.mixpanel.com *.metartmoney.com cdn.cookielaw.org *.visualwebsiteoptimizer.com *.vwo.com *.adtng.com *.atsptp.com *.spartez-software.com api.ipify.org *.s3.eu-central-1.amazonaws.com;style-src 'self' blob: 'unsafe-inline' *.cachefly.net *.b-cdn.net *.metartnetwork.com *.metart.com *.googleapis.com fonts.gstatic.com platform.twitter.com *.twimg.com maxcdn.bootstrapcdn.com *.google.com cdn.cookielaw.org *.visualwebsiteoptimizer.com *.vwo.com;font-src 'self' data: *.cachefly.net *.b-cdn.net *.metartnetwork.com *.metart.com *.zopim.com fonts.gstatic.com *.googleapis.com ssl.p.jwpcdn.com maxcdn.bootstrapcdn.com *.vwo.com;script-src 'self' 'unsafe-inline' *.cachefly.net *.b-cdn.net *.metartnetwork.com *.metart.com *.zdassets.com *.atlassian.com *.zopim.com *.twitter.com *.twimg.com ssl.p.jwpcdn.com *.googletagmanager.com *.google-analytics.com cdn.mouseflow.com *.google.com cdn.polyfill.io *.metart.network cdn.cookielaw.org code.jquery.com geolocation.onetrust.com *.mxpnl.com *.googleapis.com *.gstatic.com *.visualwebsiteoptimizer.com *.vwo.com *.adtng.com *.atsptp.com *.spartez-software.com;frame-src 'self' *.cachefly.net *.b-cdn.net *.metartnetwork.com *.metart.com *.twitter.com *.youtube.com *.vimeo.com *.atlassian.net *.metartmoney.com *.visualwebsiteoptimizer.com *.vwo.com *.google.com *.trymax.ai;img-src 'self' data: *.cachefly.net *.b-cdn.net *.metartnetwork.com *.metart.com *.icfcdn.com *.twimg.com *.twitter.com *.zopim.com *.jwpltx.com *.google-analytics.com *.gstatic.com *.googletagmanager.com *.googleapis.com *.doubleclick.net *.google.com *.visualwebsiteoptimizer.com *.vwo.com *.vscdns.com *.strpst.com *.google.com;media-src 'self' data: blob: *.cachefly.net *.b-cdn.net *.metartnetwork.com *.metart.com *.icfcdn.com *.zdassets.com *.visualwebsiteoptimizer.com *.vwo.com;worker-src 'self' data: blob: wss:;object-src 'none'
        Cross-Origin-Resource-Policy: cross-origin
        X-DNS-Prefetch-Control: off
        Expect-CT: max-age=0
        X-Frame-Options: SAMEORIGIN
        Strict-Transport-Security: max-age=15552000; includeSubDomains
        X-Download-Options: noopen
        X-Content-Type-Options: nosniff
        Origin-Agent-Cluster: ?1
        X-Permitted-Cross-Domain-Policies: none
        Referrer-Policy: no-referrer
        X-XSS-Protection: 0
        ETag: W/"f754-DouZ8uYKMHI+tko2mYZ3zihP7Fw"
        Vary: Accept-Encoding
        Content-Encoding: gzip
        X-Cache-Status: EXPIRED
```

### Outras opções:

- wappalyser
- nmap + script NSE

## Captura de email

### theHarvester
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

### Programas Python

#### EmailHarvester
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

#### email-scarper 
`$ python email-scarper.py                                                                    
[+] Enter Target URL To Scan: https://{domínio}`

### Spidefoot

`$ spiderfoot -l 127.0.0.1:5001`

Inicia o servidor local na porta 5001.<br>
Abra no navegador e acesse:

`http://127.0.0.1:5001`

Traz diversas informações como host, etc.

### Recon-ng 

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

### Matelgo

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

## Usernames
### Sherlock
O Sherlock se baseia no fornecimento, pelos designers do site, de uma URL exclusiva para um nome de usuário registrado.
Atualmente, a ferramenta é capaz de localizar usuários em mais de 300 redes sociais: Apple Developer, Arduino, Docker Hub, GitHub, GitLab, Facebook, BitCoinForum, CNET, IFTTT, Instagram, PlayStore, PyPI, Scribd, Telegram, TikTok, Tinder, etc.

`$ sherlock {user1 user2 user3}`


