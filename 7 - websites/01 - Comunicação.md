# I - HTTP request

A **mensagem que um cliente** (navegador, app móvel, script) **envia a um servidor** para pedir um recurso (página, API, arquivo). Viaja geralmente sobre **TCP** (HTTP/1.1 e HTTP/2) e, no caso do **HTTPS**, dentro de uma conexão **TLS**; no **HTTP/3** viaja sobre **QUIC/UDP**.

## Estrutura de uma HTTP request

1. **Linha de requisição (request line)**
   `<METHOD> <TARGET> <HTTP-VERSION>`
   Ex.: `GET /api/users?active=true HTTP/1.1`

2. **Cabeçalhos (headers)**
   Pares `Nome: valor` que levam metadados: tipo de conteúdo, autenticação, linguagem, cache, etc.

3. **Corpo (body)** *(opcional)*
   Os dados enviados ao servidor (JSON, formulário, arquivo, etc.). Métodos como `POST`, `PUT`, `PATCH` geralmente levam body; `GET` normalmente não.

### Exemplo 1 — GET simples

```
GET /produtos?categoria=livros&ordem=preco HTTP/1.1
Host: loja.exemplo.com
User-Agent: Mozilla/5.0
Accept: application/json
Accept-Language: pt-BR
```

### Exemplo 2 — POST com JSON

```
POST /api/login HTTP/1.1
Host: api.exemplo.com
Content-Type: application/json
Content-Length: 54

{"email":"ana@exemplo.com","senha":"minha_senha"}
```

### Exemplo 3 — Envio de arquivo (multipart/form-data)

```
POST /upload HTTP/1.1
Host: files.exemplo.com
Content-Type: multipart/form-data; boundary=abc123

--abc123
Content-Disposition: form-data; name="descricao"

Foto do recibo
--abc123
Content-Disposition: form-data; name="arquivo"; filename="recibo.jpg"
Content-Type: image/jpeg

...bytes...
--abc123--
```

## O que pode ser enviado/contido

### 1) Método (HTTP method)

* **GET** (leitura, seguro), **HEAD** (somente headers), **POST** (criar/ação),
* **PUT** (substituir), **PATCH** (alterar parcialmente), **DELETE** (remover),
* **OPTIONS** (descobrir capacidades/CORS), **TRACE**, **CONNECT** (túneis).

> *Safe*: GET/HEAD/OPTIONS; *Idempotentes*: GET/PUT/DELETE/HEAD/OPTIONS.

### 2) URL/Alvo (request target)

* **Esquema**: `http` ou `https`
* **Host** e **porta**: `api.exemplo.com:443`
* **Caminho**: `/v1/usuarios/42`
* **Query string**: `?ativo=true&page=2` (pares `chave=valor`, `&` separa; `%`-encoding para caracteres especiais)
* **Fragmento `#hash`**: **não vai ao servidor** (é só do cliente).

### 3) Headers comuns (metadados)

* **Host**: host do servidor (obrigatório em HTTP/1.1).
* **User-Agent**: cliente (navegador/bot).
* **Accept / Accept-Language / Accept-Encoding**: **negociação de conteúdo** (tipos, idioma, compressão).
* **Content-Type**: tipo do body (`application/json`, `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`, `application/xml`, binários).
* **Content-Length**: tamanho do body.
* **Authorization**: autenticação (ex.: `Bearer <token>`, `Basic <base64>`).
* **Cookie**: cookies de sessão/estado enviados ao servidor.
* **Referer** (sic) / **Origin**: origem da navegação/da requisição (usados em segurança e CORS).
* **Cache-Control**, **If-None-Match** / **ETag**, **If-Modified-Since**: controle de **cache** e validação condicional.
* **Range**: pedir apenas parte de um recurso (downloads parciais).
* **Connection** (`keep-alive`) / HTTP/2 e HTTP/3 usam **multiplexação** (sem esse header).
* **X-Forwarded-For / Forwarded**: cadeia de proxies/load balancers.

### 4) Corpo (body)

* **JSON** (APIs REST): `application/json`
* **Formulário simples** (HTML forms): `application/x-www-form-urlencoded`
* **Upload de arquivos**: `multipart/form-data`
* **Outros**: XML, protobuf, CSV, binários

> Inclua charset quando relevante: `Content-Type: application/json; charset=utf-8`.

## Detalhes importantes

* **HTTPS**: criptografa tudo (linha, headers, body).
* **CORS** (navegadores): o browser pode fazer **preflight** `OPTIONS` com `Origin`, `Access-Control-Request-Method/Headers`.
* **Cookies & Sessão**: o servidor define com `Set-Cookie` (na resposta) e o cliente retorna em `Cookie` (na requisição).
* **Idempotência e segurança**: evite efeitos colaterais em `GET`; use `POST/PUT/PATCH/DELETE` para mutação.
* **Versionamento**: `HTTP/1.1`, **HTTP/2** (binário, multiplexado), **HTTP/3** (QUIC/UDP, menos latência).

## Mini “cheat sheet”

* **Linha**: `METHOD path?query HTTP/1.1`
* **Obrigatórios típicos**: `Host`, `Content-Type` (se tiver body), `Authorization` (se protegido)
* **Negociação**: `Accept`, `Accept-Language`, `Accept-Encoding`
* **Cache**: `Cache-Control`, `If-None-Match`/`ETag`
* **CORS (browser)**: `Origin`, `OPTIONS` preflight
* **Body**: JSON para APIs, `multipart` para arquivos, `x-www-form-urlencoded` para forms

# II - HTTP **response**

A **mensagem que o servidor envia de volta** ao cliente (navegador, app, script) após receber uma request. Carrega o **status do resultado**, **metadados** (headers) e, frequentemente, um **corpo** (body) com o recurso/JSON/arquivo.

## Estrutura da HTTP response

1. **Status line**
   `HTTP/<versão> <status-code> <reason-phrase>`
   Ex.: `HTTP/1.1 200 OK`

> Em HTTP/2 e HTTP/3 a razão é opcional no “fio”, mas a semântica é a mesma.

2. **Headers**
   Pares `Nome: valor` com metadados (cache, cookies, CORS, segurança, etc.).

3. **Body** *(opcional)*
   O conteúdo em si: HTML, JSON, binário, stream, etc.

> **Sem body** em **1xx**, **204 No Content**, **304 Not Modified** e **HEAD**.

### Códigos de status (visão geral)

* **1xx** Informativo (ex.: `100 Continue`)
* **2xx** Sucesso (`200 OK`, `201 Created`)
* **3xx** Redirecionamento (`301`, `302`, `304 Not Modified`)
* **4xx** Erro do cliente (`400`, `401`, `403`, `404`, `429`)
* **5xx** Erro do servidor (`500`, `502`, `503`, `504`)

## Headers de resposta — os mais úteis

**Representação & transporte**

* `Content-Type`: tipo do corpo (`application/json; charset=utf-8`, `text/html`, `image/png`).
* `Content-Length` ou `Transfer-Encoding: chunked` (streaming).
* `Content-Encoding`: compressão (`gzip`, `br`, `zstd`).
* `Content-Language`, `Content-Disposition` (ex.: `attachment; filename="relatorio.pdf"`).

**Cache & validação**

* `Cache-Control` (`no-store`, `max-age=300`, `public/private`).
* `ETag` + `Last-Modified`; combinam com requests condicionais (`If-None-Match`, `If-Modified-Since`) e permitem **304**.
* `Vary` (p. ex. `Vary: Accept-Encoding, Origin`) para caches/CDNs.
* `Expires`, `Age`.

**Redirecionamento & criação**

* `Location`: destino de redireciono (**3xx**) ou URL do recurso criado (**201 Created**).

**Autenticação & autorização**

* `WWW-Authenticate`: esquema exigido (ex.: `Bearer realm="api"`).
* `Set-Cookie`: definir cookies (use atributos `Secure; HttpOnly; SameSite=Lax/Strict`).

**CORS (navegadores)**

* `Access-Control-Allow-Origin`, `-Methods`, `-Headers`, `-Credentials`, `-Max-Age`.

**Range / downloads**

* `Accept-Ranges: bytes` e, em **206**, `Content-Range: bytes 0-1023/4096`.

**Rate limit & retry**

* `Retry-After: 120` (em **429**/**503**), `X-RateLimit-*` (convenções comuns).

**Segurança**

* `Strict-Transport-Security` (HSTS), `Content-Security-Policy`, `X-Content-Type-Options: nosniff`,
* `Referrer-Policy`, `Permissions-Policy`, `Cross-Origin-Opener-Policy`.

**Observabilidade/infra**

* `Date`, `Server`, `Via`, `Traceparent`/`X-Request-Id`, `Link` (paginação).

### Exemplos rápidos (raw)

#### 200 OK (JSON comprimido)

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Encoding: br
Cache-Control: public, max-age=60
ETag: "abc123"
Vary: Accept-Encoding

{"data":[...]}
```

#### 201 Created (com Location)

```
HTTP/1.1 201 Created
Location: /v1/produtos/987
Content-Type: application/json

{"id":987,"nome":"Livro X"}
```

#### 301 Redirect

```
HTTP/1.1 301 Moved Permanently
Location: https://novo.dominio.com/recurso
```

#### 304 Not Modified (sem body)

```
HTTP/1.1 304 Not Modified
ETag: "abc123"
```

#### 206 Partial Content (download por faixa)

```
HTTP/1.1 206 Partial Content
Accept-Ranges: bytes
Content-Range: bytes 0-1023/4096
Content-Type: application/pdf

...1024 bytes...
```

#### 401 Unauthorized (desafio de auth)

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer realm="api", error="invalid_token"
```

#### 429 Too Many Requests (rate limit)

```
HTTP/1.1 429 Too Many Requests
Retry-After: 60
```

#### CORS liberando origem específica

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://app.cliente.com
Vary: Origin
```

### Corpo (body): formatos comuns

* **APIs**: `application/json` (às vezes `application/problem+json` para erros padronizados)
* **Formulários/arquivos**: `multipart/form-data`
* **Páginas**: `text/html`
* **Binários**: imagens, PDFs, streams de vídeo/áudio
* **Sem corpo**: 204/304/HEAD/1xx

### Mini “cheat sheet”

* **Linha de status** diz o **resultado**; **headers** definem **como tratar**; **body** traz o **conteúdo**.
* Use **cache com ETag/Last-Modified**; defina **CSP/HSTS**; cookies com `Secure`/`HttpOnly`/`SameSite`.
* **Location** em 201/3xx; **Retry-After** em 429/503; **CORS** só quando há front-end em domínio diferente.


