# Registro de manutenção

Histórico de tudo o que foi corrigido, implementado ou investigado na plataforma:
qual era o problema, a causa e como foi resolvido. Serve para quem assumir o
projeto saber o que já aconteceu e por quê.

## Como registrar

1. Copie [`_modelo.md`](_modelo.md) para um arquivo novo chamado
   `AAAA-MM-DD-assunto-curto.md` (data em que o problema foi identificado).
2. Preencha as seções. Se algo ainda não se sabe, escreva "não confirmado".
3. Adicione uma linha na tabela abaixo, a mais recente no topo.
4. O registro entra **no mesmo PR** da correção ou feature.
5. Quando o status mudar (ex.: de "em andamento" para "resolvido"), atualize o
   arquivo e a tabela.

Status possíveis: `aberto`, `em andamento`, `resolvido`, `descartado`.

## Registros

| Data | Tipo | Assunto | Status |
|---|---|---|---|
| 2026-11-04 | segurança | [Path traversal na rota catch-all expõe arquivos do servidor](2026-11-04-path-traversal-catchall.md) | em andamento |
| 2026-10-22 | incidente | [Serviço suspenso no Render](2026-10-22-servico-suspenso-render.md) | resolvido |

## Pendências conhecidas

Problemas identificados no review de outubro/2026 que ainda não têm registro
próprio. O review foi feito antes da reorganização em `backend/` e `frontend/`,
então confirme no código atual antes de corrigir. Ao começar a resolver um
deles, crie o registro e tire daqui.

- Falta de isolamento entre UBSs nas funções `_get_*_or_404` e em
  `gestor_ubs_routes`: um gestor pode acessar dados de outra UBS pelo id.
- Reset de senha por GESTOR sem restrição de UBS.
- `PATCH` de agendamento não confere se o agendamento é do usuário.
- State e token do OAuth trafegam na URL.
- Dependências do frontend com alertas no `npm audit`.
