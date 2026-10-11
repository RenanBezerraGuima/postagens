https://github.com/bjarneo/cliamp/pull/618

Suporte aos links curtos do SoundCloud. Ao resolver playlists, o yt-dlp pode retornar faixas com URLs da API e títulos que são apenas números. O cliamp agora usa esses IDs para buscar título, artista, duração e permalink corretos. Também ajustei a ajuda do gerenciador de playlists: `w` salva a fila inteira na tela principal e copia faixas selecionadas quando estou dentro de uma playlist.

Rodei `make check` — formatação, `go vet` e testes de todos os pacotes — e passou. O commit `724e283b` já foi enviado para a branch do PR.
