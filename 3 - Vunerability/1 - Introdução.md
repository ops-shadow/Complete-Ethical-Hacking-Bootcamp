# pesquisa de vulnerabilidades

Momento em que, após o mapeamento e a varredhttps://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/edit/main/3%20-%20Vunerability/1%20-%20Introdu%C3%A7%C3%A3o.mdura do alvo, o pentester passa a identificar **quais falhas de segurança conhecidas** podem estar presentes nos sistemas analisados.

## Objetivo:

Transformar os dados brutos coletados (IPs, portas abertas, serviços, versões de software, frameworks, etc.) em **possíveis pontos fracos exploráveis**, comparando-os com bancos de dados de vulnerabilidades conhecidas.

## Principais atividades

1. **Identificação de versões de software e serviços**
   – A partir do fingerprinting feito na fase de varredura, verifica-se qual versão de servidor web, banco de dados, sistema operacional ou aplicação está em uso.

2. **Correlação com vulnerabilidades conhecidas**
   – Consulta a bancos de dados como:

   * CVE (Common Vulnerabilities and Exposures)
   * NVD (National Vulnerability Database)
   * Exploit-DB
   * OWASP para falhas web comuns (SQLi, XSS, CSRF, etc.)

3. **Uso de scanners de vulnerabilidade**
   – Ferramentas como **Nessus, OpenVAS, Nikto, Nmap NSE scripts, Burp Suite, OWASP ZAP**, que ajudam a automatizar a detecção de falhas.

4. **Análise manual e validação**
   – O pentester não depende só de ferramentas: faz também testes manuais para confirmar resultados (ex.: inputs maliciosos em formulários, análise de headers, configuração incorreta de TLS/SSL).

## Resultado esperado

No fim dessa fase, o pentester terá:

* Uma lista priorizada de vulnerabilidades prováveis.
* Informações técnicas sobre cada vulnerabilidade (criticidade, impacto e exploitabilidade).
* Base para avançar à fase seguinte: **exploração**.
