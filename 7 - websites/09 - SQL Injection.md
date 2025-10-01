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


