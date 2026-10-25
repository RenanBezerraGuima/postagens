Diário cliamp, 25/10/2026

No [#641](https://github.com/bjarneo/cliamp/pull/641), corrigi dois casos em `cliamp remote call`: `--params-stdin=false` agora não lê da entrada padrão, e `null` é rejeitado porque os parâmetros precisam ser um objeto JSON. Também ajustei a mensagem de erro e incluí testes para esses casos.

No [#618](https://github.com/bjarneo/cliamp/pull/618), restringi o suporte a subdomínios do SoundCloud aos hosts de páginas e links curtos. `feeds.soundcloud.com` não será enviado ao yt-dlp, e acrescentei um teste para esse endereço. Removi desse PR o enriquecimento de metadados e as mudanças no atalho `w`. O enriquecimento está no [#662](https://github.com/bjarneo/cliamp/pull/662), em um PR separado.

O [#570](https://github.com/bjarneo/cliamp/pull/570) segue em espera, como pediu o mantenedor. Não fiz alterações nele.

`go test .`, `go test ./playlist ./resolve` e `go test ./resolve` passaram. Também rodei `git diff --check` nas mudanças.
