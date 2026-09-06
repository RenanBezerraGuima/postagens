Aprofundando no Omarchy https://github.com/omacom/omarchy, baseado em Arch Linux, usa o Hyprland como gerenciador de janelas e tem uma interface própria feita em Quickshell (com QML e JavaScript). Como já é o sistema que eu venho usando no meu computador todo dia, achei que fazia todo sentido aproveitar essa proximidade para contribuir de verdade com o projeto.

Antes de sair abrindo arquivos de código e tentando mudar coisas, gastei um tempo lendo a documentação interna deles para entender o fluxo de contribuição. 

Descobri algumas coisas específicas da arquitetura deles:

  • Nunca mexer direto em /usr/share/omarchy: O sistema tem um comando próprio (omarchy dev link <caminho-do-clone>) que cria uma configuração em /etc/omarchy.conf e regras de sudo para apontar o ambiente para a pasta onde você clonou o código. Isso permite testar modificações no sistema em execução sem quebrar os pacotes instalados pelo gerenciador de pacotes.
  • Padrões de Shell Script: Eles são exigentes com Bash. O shebang precisa ser estritamente #!/bin/bash, a indentação é de dois espaços, comparações de texto e arquivos sempre usam [[ ]], e qualquer coisa numérica tem que usar (( )). Eles também evitam verificações defensivas desnecessárias se o comando já faz parte do pacote padrão.
  • Testes automatizados: Eles têm uma bateria de testes em ./test/cli e ./test/shell. O que achei interessante é que, para os scripts em JavaScript que rodam dentro do menu, eles usam um adaptador com Node.js para rodar testes unitários rápidos direto pelo terminal, sem precisar abrir janela ou subir compositor gráfico.

Para não me perder depois, resumi essas regras em um documento local para consultar durante o semestre.

Com essa base em mãos, fui dar uma olhada na aba de Discussions do repositório https://github.com/omacom/omarchy/discussions/categories suggestions para ver o que os usuários estavam sugerindo ou sentindo falta.

Lá me deparei com a discussão #7237: Inline calculator in the Omarchy menu (regression from 3.8) https://github.com/omacom/omarchydiscussions/7237

O ponto dele era muito bom: na versão 3.8 do Omarchy, eles usavam outro launcher (Walker/Elephant) que tinha uma extensão de calculadora embutida. Quando você apertava o atalho do menu (Super + Espaço) e digitava algo como 357/2, o resultado aparecia ali na hora. Mas na versão 4.0, quando reescreveram o menu em Quickshell (shell/plugins/menu/Menu.qml), essa funcionalidade simplesmente não foi recriada. Ter que abrir um app de calculadora separado só para fazer uma conta de dois segundos quebra o fluxo de quem está lendo um texto ou programando.

Achei que seria uma ótima oportunidade de implementação, já que a lógica de busca do menu fica concentrada em um arquivo JavaScript puro (shell/plugins/menu/MenuModel.js).


Tinha deixado a intenção de criar a feature para o resto da semana.

Uma coisa que venho aprendendo sobre software livre é que, antes de sentar e começar a programar do zero, vale muito a pena investigar se alguém já não está trabalhando na mesma coisa. Se eu não tivesse olhado, teria perdido horas com retrabalho.

Chequei os Pull Requests abertos no repositório (omacom/omarchy) e vi duas coisas:

  1. Tinha um PR mais antigo (#8537 https://github.com/omacom/omarchy/pull/8537) tentando criar um sistema bem mais complexo de plugins externos em Python.
  2. E, para minha surpresa, ontem um desenvolvedor abriu o PR #10294: feat(menu): inline calculator in search https://github.com/omacom/omarchy/pull/10294.

Ele implementou um analisador matemático direto em JavaScript dentro de MenuModel.js, sem depender de processos externos rodando a cada tecla digitada. Suporta operadores básicos (+, -, *, /), símbolos usuais (x, ×, ÷, %), parênteses, decimais e multiplicação implícita (como 2(3+4)). O resultado aparece centralizado no topo do menu e, ao apertar Enter, o valor é copiado para a área de transferência via wl-copy. Além disso, ele já mandou 30 testes unitários cobrindo casos de borda.

Testei a feature no próprio sistema para ver se funcionava e como seria o processo de testar uma feature.

1. Puxei o branch do PR pelo GitHub CLI: gh pr checkout 10294 --branch test-pr-10294

2. Rodei a suíte de testes do PR (bash test/shell.d/menu-calc-test.sh), e todos os 30 testes passaram de primeira.


3. Vinculei o meu clone local ao sistema com o omarchy dev link e reiniciei a sessão para carregar o Quickshell modificado.


4. Abri o menu no atalho do teclado e testei várias contas: 12x12 deu 144, 357/2 deu 178.5, porcentagens como 50% e 5%200 calcularam certo, e buscas normais por programas continuaram funcionando como deveriam.


5. Depois dos testes, desfiz a vinculação (omarchy dev unlink) e voltei a minha máquina para o branch estável quattro.


A discussão original (#7237 https://github.com/omacom/omarchy/discussions/7237) estava aberta há algumas semanas sem nenhuma resposta, o que dava a impressão de que a sugestão tinha sido esquecida.

Para ajudar a conectar as pontas na comunidade, deixei um comentário lá avisando o autor e os outros interessados sobre a existência do PR #10294, relatando que fiz os testes no meu ambiente e que o código está funcionando muito bem:

Essa semana me mostrou que colaborar em código aberto não é somente abrir PR e subir commit.

Fazer uma boa triagem, testar o código que outros desenvolvedores mandaram e manter as discussões da comunidade atualizadas também economiza tempo de todo mundo e ajuda o projeto a andar.

Para a próxima semana, vou acompanhar se o PR #10294 vai ser aceito e já estou garimpando outra issue ou funcionalidade que ainda não tenha ninguém trabalhando para poder contribuir com código.