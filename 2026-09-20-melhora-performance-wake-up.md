# Diário — suspend/resume e PRs (set/2026)

## 1. Lock sem foco após o resume (precisava do touchpad)

Após acordar do suspend, a tela de lock não aceitava o que eu digitava: era preciso clicar na tela (touchpad) antes da senha registrar.
Procurei PRs abertos e achei o [omacom/omarchy#12435](https://github.com/omacom/omarchy/pull/12435) ("Reclaim lock screen keyboard focus after resume from suspend", de jonathan-downs), que adiciona o monitor de resume via systemd + verbo `resume()` no IPC do lock.
Testei na minha máquina e resolveu: após o resume, a senha entra direto, sem clique.
Deixei comentário de apoio: [issuecomment-5750251766](https://github.com/omacom/omarchy/pull/12435#issuecomment-5750251766).

## 2. Wake do suspend demorado (trabalho próprio)

Outro sintoma: o acordar do suspend era lento. Procurei PRs abertos sobre isso e não encontrei — então fizemos o trabalho no branch `perf/wake-latency`:

- Diagnóstico: `bin/omarchy-system-wake` rodava 3 passos em sequência — `brightness-display on` (acende o painel, crítico), `brightness-keyboard restore` e `hyprland-monitor-clamshell` (pode levar segundos em dock, via `hyprctl reload`).
- Mudança ([commit 5b76c3a9](https://github.com/RenanBezerraGuima/omarchy/commit/5b76c3a9)): display continua em foreground; teclado e clamshell vão para background. Efeito colateral corrigido: um clamshell lento segurava o `wakeProcess` do QML e fazia o wake seguinte ser descartado por inteiro (inclusive o DPMS enable).
- Números (5 runs, mesma máquina): mediana **151ms → 31ms** (~5x).
- Testes: `test/shell.d/monitor-recovery-test.sh` 18/18 e `test/cli` verdes, antes e depois; verificação manual (lock→unlock, `dpmsStatus: true`, sem zumbis).
- PR aberto: [omacom/omarchy#12634](https://github.com/omacom/omarchy/pull/12634) (do fork `RenanBezerraGuima/omarchy`, branch `perf/wake-latency`).
- Follow-up deixado de fora de propósito: o `sleep 4` + transições `supergfxctl` síncronas no hook `post` do `force-igpu` também seguram o resume, mas mudar isso quebra as expectativas síncronas de `system-sleep-ownership-migration-test.sh` — fica para outro PR.

## 3. Calculadora no menu (semana passada)

Trabalho da semana passada: [omacom/omarchy#10294](https://github.com/omacom/omarchy/pull/10294) ("feat(menu): inline calculator in search", de aragi-dev) — expressão aritmética na busca do menu mostra o resultado primeiro; Enter copia e fecha.
Testei e funcionou; comentário de apoio: [issuecomment-5750251780](https://github.com/omacom/omarchy/pull/10294#issuecomment-5750251780).
Branch do PR baixado nesta máquina como `pr-10294-menu-calc` (via `git fetch origin pull/10294/head`); branch atual continua `perf/wake-latency`.
