# Serviço suspenso no Render

- **Data:** 2026-10-22
- **Tipo:** incidente
- **Status:** resolvido (2026-11-09)
- **Responsável:** Allyson Kawã
- **PR / commits:** nenhum (problema de infraestrutura, não de código)

## Problema

O cliente tentou acessar https://plataforma-virtual.onrender.com e viu a página
"This service has been suspended by its owner". O site continuava fora do ar em
2026-11-03 e 2026-11-09.

## Causa

Não confirmada. O Render responde `503` com o header
`x-render-routing: suspend-by-user` em cerca de 1s, então não é o serviço
dormindo nem erro da aplicação: a suspensão foi feita na conta do Render.

Hipóteses:

- **Horas grátis esgotadas** (750h/mês, consumidas pelo keep-alive 24/7 em
  `backend/main.py`). Pouco provável: virou o mês e o serviço não voltou.
- **Suspensão manual** por alguém com acesso ao painel.
- **Cobrança**: cartão recusado ou fatura pendente.

## Solução

Samuel Fortes resolveu no Render em 2026-11-09. A plataforma voltou em um
**endereço novo**, acompanhando a mudança de nome do projeto:

- Novo (no ar): https://meu-territorio.onrender.com
- Antigo (continua suspenso): https://plataforma-virtual.onrender.com

Quem usava o endereço antigo precisa ser avisado do novo.

O motivo da suspensão do serviço antigo e se ele foi substituído de propósito
ainda não estão registrados: perguntar ao Samuel e completar aqui.

## Como verificar

Em 2026-11-09, `https://meu-territorio.onrender.com` respondeu `200` em `/`,
`/login` e `/ping`, e `/health` devolveu
`{"status":"ok","database":"connected"}`.

## Pendências

- Confirmar com o Samuel a causa da suspensão e se o serviço antigo será
  apagado ou redirecionado.
- Confirmar se os segredos do serviço novo foram trocados (ver
  [2026-11-04-path-traversal-catchall.md](2026-11-04-path-traversal-catchall.md)).
