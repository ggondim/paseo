# Readaloud (TTS por turno) no Paseo: tentativa upstream — encerrada

Data: 2026-10-09
Status final: abandonado (decisão do usuário). Pivot para STT/TTS direto via Apple Watch, fora deste repo.

## Objetivo

Ter saída de voz por turno no Paseo: um botão de "falar" no rodapé de cada mensagem do agente, que lê em voz alta a conclusão do turno reusando o TTS já configurado, sem depender do voice mode (que é frágil e quebra, bug getpaseo/paseo#3239).

## Linha do tempo e o que foi feito

1. Fork `getpaseo/paseo` criado em `ggondim/paseo`, clonado em `~/repos/ggondim/paseo`, worktree isolada `feat/readaloud-por-turno` criada para o trabalho.
2. Mapeamento de viabilidade (sub-agente): o motor de TTS (`TTSManager`) já é desacoplado de mic/VAD, e o cliente já sabe tocar áudio marcado como `isVoiceMode: false`. Conclusão: fork pequeno viável.
3. Pesquisa nos canais oficiais (GitHub Discussions e Discord) antes de construir. Descoberta decisiva: a feature já existia como PR aberto `getpaseo/paseo#2675` ("Read aloud: speak an agent turn from its footer", autor kaspesi), quase idêntica ao que íamos fazer, inclusive no mesmo ponto de inserção (rodapé do turno).
4. Adoção do #2675 em vez de construir do zero: checkout no worktree, teste contra a nossa infra TTS real (openedai-speech / XTTS em tatooine) funcionando ponta a ponta, e rebase limpo contra `upstream/main`.
5. Comentário postado no PR #2675 (conta ggondim) oferecendo o rebase.
6. Maintainer (boudra) fechou o #2675 e redirecionou para Discussions, conforme o CONTRIBUTING.md (decisões de produto só pelo maintainer, via Discussion ou #product no Discord).
7. Comentário postado na Discussion `getpaseo/paseo#4248` ("Continuous text-to-speech playback"), enquadrado como workflow, somando a nossa versão do problema.
8. Heartbeat de monitoramento da #4248 configurado (diário, depois semanal) via Paseo.
9. Quatro semanas sem tração na #4248 (1 upvote, zero respostas). Socialização no Discord ficou travada em onboarding gated do server. Usuário decidiu abandonar o empurrão upstream.

## Decisões tomadas

1. Buscar nos canais antes de codar. Evitou reconstruir uma feature que já existia pronta (#2675).
2. Adotar o PR de terceiro em vez de fork paralelo. Caminho mais rápido para a feature e para virar contribuidor upstream.
3. Requisito do usuário: read-aloud nunca deve depender do voice mode estar habilitado. Observação importante: o #2675 reusa `features.voiceMode.tts`, ou seja, read-aloud não precisa do voice mode ligado (imune ao #3239), mas precisa dele configurado. A comunidade (issacfriedman) pediu explicitamente "sem segunda config de TTS", o que vai contra desacoplar a config.
4. Respeitar o processo do projeto: nada de empurrar commit em PR de terceiro, discussão de produto só via Discussions/Discord.
5. Abandonar: sem tração orgânica e sem acesso ao canal de socialização, o retorno não justifica o esforço. Deixar o maintainer tocar a feature sozinho.

## Aprendizados sobre o projeto e o processo

1. O processo do Paseo é opinativo: todas as decisões de produto, design e arquitetura são do maintainer. Feature requests como issue são fechadas automaticamente. O canal certo é Discussion/Discord, enquadrado como workflow ("o que você tenta fazer"), não como "construa X".
2. O que o maintainer lê para decidir o que construir é o acúmulo de interesse numa Discussion. Sem upvotes/comentários de terceiros, uma Discussion não sobe.
3. Arquitetura relevante do read-aloud: `TTSManager` (packages/server/.../agent/tts-manager.ts) é puro, sem mic/VAD; o cliente toca áudio `isVoiceMode:false` por um caminho que já existe; o acoplamento com mic/turn-controller fica todo acima do TTSManager.
4. O rebase revelou três pontos que o PR original não previa: `@getpaseo/client` estreitou a API pública (importar `DaemonClient` de `/internal/daemon-client`), `operation-permissions.ts` passou a ser exaustivo (precisa das entradas `speech.tts.read_aloud.*`), e um locale novo (`ko`) exige as chaves de tradução.

## Estado dos artefatos

| Artefato | Situação |
|----------|----------|
| Fork `ggondim/paseo` (GitHub) | Mantido |
| Comentários em #2675 e #4248 | Mantidos (públicos, inofensivos) |
| Branches `pr-2675`, `pr-2675-orig`, `feat/readaloud-por-turno` | Removidos nesta limpeza |
| Worktree `feat-readaloud-por-turno` | Já desregistrada do git, prunada |
| Handoff `~/paseo-readaloud-feature-handoff.md` | Removido nesta limpeza |
| Heartbeat de monitoramento da #4248 | Removido |

Observação: o rebase limpo do #2675 estava no branch `pr-2675` (compilava, lint e 228 testes verdes). Ao remover o branch, esse trabalho não fica mais acessível localmente. Para recuperar no futuro, refazer o checkout do PR a partir do upstream e reaplicar o rebase (os três pontos acima são o roteiro).

## Próximos passos

Nenhum neste repo. O usuário seguiu para uma solução de STT/TTS direto pelo Apple Watch, em projeto separado. A feature read-aloud fica por conta do maintainer do Paseo, via Discussion #4248.
