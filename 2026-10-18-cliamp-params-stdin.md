Diário cliamp, 18/10/2026

Fui procurar issues do cliamp que ainda não tinham sido resolvidas e encontrei a #571: o comando `cliamp remote call` só aceitava os parâmetros da operação pela opção `--params`. Como o JSON era passado como argumento, consultas, nomes de faixas e outros metadados podiam aparecer na lista de processos ou em `/proc/<pid>/cmdline`, inclusive para outros usuários da máquina.

Propus a solução no PR #641: https://github.com/bjarneo/cliamp/pull/641. Agora é possível passar os parâmetros com `--params-file <caminho>` ou pela entrada padrão com `--params-stdin`. Também dá para usar `--params-file -` como alternativa para ler de stdin. A opção `--params` continua disponível para chamadas simples.

O comando rejeita combinações de mais de uma dessas opções, entradas vazias, JSON inválido ou que não seja um objeto, e conteúdo maior que 1 MiB. Se stdin estiver ligado a um terminal, ele encerra com uma mensagem clara em vez de ficar esperando entrada. Atualizei também a documentação do controle remoto e incluí testes unitários e de integração para esses caminhos.
