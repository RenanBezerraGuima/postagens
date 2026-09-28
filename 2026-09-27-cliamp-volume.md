Diário cliamp, 27/09/2026

A pasta foi configurada no esquema de fork. O clone aponta para o meu fork como origin e tem o repositório original como upstream. Funciona assim porque não tenho escrita no projeto do bjarneo, então o trabalho acontece num branch do fork e chega ao original via pull request. O upstream serve para manter o fork sincronizado com o main de lá antes de abrir qualquer PR.

Passei a primeira parte lendo o projeto. É um player de terminal em Go com Bubbletea, o diretório maior é o ui/model, os providers ficam em external/ e a config é um TOML. No histórico contei 1038 commits em conventional commits, e o template de PR cobra make check mais atualização de docs/ e site/ em mudança visível.

A contribuição foi fazer o volume sobreviver ao reinício. O valor já era lido do config na abertura, só que nada escrevia de volta. Implementei o salvamento com debounce de 1 segundo, copiando o que speed e EQ já faziam, cobrindo teclas, IPC, MPRIS e o set_volume do Lua, que passei a rotear pelo model. Testes novos no volume_persist_test.go e a suíte inteira verde.

Para testar à mão criei o test-volume-persist.sh. Ele compila, roda com config isolado em /tmp e mostra o volume salvo ao sair. O teste confirmou que funciona, até saindo logo em seguida por causa do flush no quit.

Depois garimpei o GitHub e achei o PR #265, que tentava exatamente isso dentro de um pacotão de 8 features e foi fechado sem merge. Não havia nenhuma issue pedindo a feature, então citei o #265 na descrição e abri o PR #570 contra o upstream: https://github.com/bjarneo/cliamp/pull/570

O CodeRabbit revisou o PR automaticamente e deixou dois apontamentos que valiam atender. Um deles notava que, se a escrita no config falhasse, o salvamento pendente era descartado em silêncio. Mudei o saveVolume para retornar erro e o tick passou a rearmar o debounce para tentar de novo. O outro pedia testes passando pelos caminhos reais do Update, então adicionei subcasos de tick e quit mais um teste de retry com saver que falha. O terceiro aviso, sobre cobertura de docstrings, ignorei por ser um check genérico que não se aplica ao estilo do projeto. Subi tudo num segundo commit e o PR ficou verde de novo.
