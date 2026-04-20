
# Compliments
Frases usadas no MagicMirror

nano ~/MagicMirror/modules/MMM-Remote-Control/node_helper.js

Comando liga / desliga tela 
***************************************************************************

Ah! Esse log foi a peça que faltava. Ele revelou exatamente o que está acontecendo e eu peço desculpas, pois te induzi ao erro na etapa do config.js.

O log mostra que o botão padrão do módulo tem o comando antigo (tvservice -o) escrito diretamente no código-fonte dele. Ou seja, ele ignorou completamente os comandos customizados que colocamos no config.js e teimosamente tentou usar o padrão dele, que não existe mais no seu sistema (por isso o erro "não encontrado" / código 127).

Para consertar isso de vez, nós vamos ter que fazer uma pequena cirurgia no "cérebro" do módulo (o arquivo node_helper.js) e trocar o comando lá dentro.

Aqui está o passo a passo de como fazer isso de forma segura:

Passo 1: Abrir o arquivo de código do módulo
No terminal do seu Raspberry Pi (via SSH), digite o seguinte comando para abrir o editor de texto:

Bash
---- nano ~/MagicMirror/modules/MMM-Remote-Control/node_helper.js ------
Passo 2: Encontrar e substituir o comando de Desligar
Com o arquivo aberto, aperte Ctrl + W (para abrir a barra de pesquisa).

Digite tvservice -o e aperte Enter.

O cursor vai pular exatamente para a linha do erro. Você verá algo parecido com isso:
exec("/opt/vc/bin/tvservice -o", opts, function(error, stdout, stderr) {

Apague a parte do /opt/vc/bin/tvservice -o e substitua pelo comando que sabemos que funciona para você: vcgencmd display_power 0.
A linha deverá ficar assim:
exec("vcgencmd display_power 0", opts, function(error, stdout, stderr) {

Passo 3: Encontrar e substituir o comando de Ligar
Agora vamos arrumar o botão de ligar, que sofre do mesmo problema.

Aperte Ctrl + W novamente.

Digite tvservice -p e aperte Enter.

Ele vai pular para o comando de ligar. Você verá algo longo, mais ou menos assim:
exec("/opt/vc/bin/tvservice -p && sudo chvt 6 && sudo chvt 7", opts, function...

Apague todo esse conteúdo entre as aspas e substitua pelo comando de ligar: vcgencmd display_power 1.
A linha deverá ficar assim:
exec("vcgencmd display_power 1", opts, function(error, stdout, stderr) {

Passo 4: Salvar e Reiniciar
Aperte Ctrl + O (letra O) para salvar.

Aperte Enter para confirmar o nome do arquivo.

Aperte Ctrl + X para fechar o editor.

Por fim, reinicie o MagicMirror no terminal para que ele carregue o novo código:

Bash
pm2 restart MagicMirror
Como nós alteramos o código direto na raiz do módulo, agora quando você apertar o botão "Power" no celular, ele será forçado a disparar o vcgencmd e a tela finalmente vai desligar e ligar. Pode testar!

************************************************************************

<img width="848" height="661" alt="Captura de Tela 2026-04-20 às 02 24 09" src="https://github.com/user-attachments/assets/dd5d243e-c648-4622-8103-3ca33c3ec9b6" />
<img width="846" height="708" alt="Captura de Tela 2026-04-20 às 02 24 26" src="https://github.com/user-attachments/assets/b5d0b7eb-725f-4c5d-b09d-bf08583bdaf4" />
<img width="799" height="359" alt="Captura de Tela 2026-04-20 às 02 24 45" src="https://github.com/user-attachments/assets/d44b5d0c-4765-41e9-80e2-b971598cc694" />



