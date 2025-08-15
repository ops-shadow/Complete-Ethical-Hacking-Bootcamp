# **Fluxo de Reconnaissance com Ferramentas OSINT no Kali Linux**

## **🎯 Objetivo**

* Descobrir **endereços de e-mail, subdomínios, IPs, perfis sociais, registros WHOIS e metadados** usando apenas **técnicas passivas** (não interagir diretamente com o servidor, para não gerar alerta no alvo).

---

## **1️⃣ Definir o escopo**

Antes de começar:

* **Domínio-alvo:** `exemplo.com`
* Escopo: apenas domínio principal e subdomínios
* Restrições: somente **coleta passiva**

---

## **2️⃣ Coleta inicial — *theHarvester***

**Objetivo:** Lista rápida de e-mails, subdomínios e hosts.

```bash
theHarvester -d exemplo.com -b all -l 500 -f theharvester_result.html
```

* `-b all`: todas as fontes disponíveis
* `-l 500`: até 500 resultados por fonte
* `-f`: salva relatório HTML

📌 **Saída:** Primeira lista de e-mails e subdomínios para usar nas próximas ferramentas.

---

## **3️⃣ Varredura multi-fonte — *SpiderFoot***

**Objetivo:** Explorar mais de 100 fontes de dados automaticamente.

```bash
spiderfoot -l 127.0.0.1:5001
```

* Acesse `http://127.0.0.1:5001`
* Novo Scan → Alvo: `exemplo.com`
* Tipo: **Passive**
* Rodar → Filtrar resultados por:

  * **Email Address**
  * **IP Address**
  * **Domain Name**
  * **Social Media**

📌 **Saída:** Relatório com dados mais amplos (incluindo APIs como `crt.sh` para certificados SSL).

---

## **4️⃣ Coleta modular e filtrada — *Recon-ng***

**Objetivo:** Buscar e-mails e domínios relacionados usando módulos específicos.

```bash
recon-ng
```

Dentro do Recon-ng:

```text
workspaces create exemplo
add domains exemplo.com
marketplace install recon/domains-contacts/whois_pocs
marketplace install recon/domains-contacts/bing_domain_web
marketplace install recon/domains-contacts/google_site_web
modules load recon/domains-contacts/whois_pocs
run
modules load recon/domains-contacts/bing_domain_web
run
modules load recon/domains-contacts/google_site_web
run
show contacts
```

📌 **Saída:** Lista consolidada de e-mails e possíveis nomes de funcionários.

---

## **5️⃣ Análise visual e conexões — *Maltego***

**Objetivo:** Criar um mapa visual ligando domínio, e-mails, IPs e perfis.

1. Abra:

   ```bash
   maltego
   ```
2. Novo Grafo → Adicione **Domain** → `exemplo.com`
3. Clique com botão direito → **Run Transform**:

   * `To Email Address [Common]`
   * `To Email Address [Search Engine]`
   * `To DNS Name - Brute Force`
   * `To Domain WHOIS Record`
4. Expanda cada entidade para encontrar conexões.

📌 **Saída:** Mapa visual de relacionamentos que facilita entender a infraestrutura e pontos de entrada.

---

## **6️⃣ Consolidação de dados**

Juntar tudo em um único arquivo:

```bash
cat theharvester_result.html spiderfoot_emails.txt reconng_emails.txt maltego_emails.txt \
| grep -E -o "\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b" \
| sort -u > recon_emails_final.txt
```

* Remove duplicatas
* Gera **lista final de e-mails** para fases seguintes (engenharia social, spear phishing, etc.)

---

## **Fluxo visual resumido**

```
theHarvester  →  SpiderFoot  →  Recon-ng  →  Maltego
   |                |             |           |
   └── E-mails ─────┴── Subdomínios ───────────┘
                      ↓
               Consolidação final
```

---

💡 **Próximo passo no pentest:**
Com a lista de e-mails, subdomínios e IPs, você pode:

* Fazer **enumeração de subdomínios** com *Amass* ou *Sublist3r*.
* Identificar **serviços expostos** com *Nmap*.
* Usar os e-mails para buscar **senhas vazadas** (*HaveIBeenPwned*, *Dehashed*).

---

Se quiser, posso já criar **um script Bash** que executa todas essas ferramentas automaticamente e gera um relatório unificado de reconnaissance no Kali. Isso deixaria o processo todo em um comando só. Quer que eu monte?
