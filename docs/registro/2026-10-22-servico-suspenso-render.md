# Serviço suspenso no Render

- **Data:** 2026-10-22
- **Tipo:** incidente
- **Status:** aberto
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

Pendente. Passos:

1. No painel do Render (https://dashboard.render.com), abrir o serviço e ver o
   aviso no topo e o log de **Events** para saber quem suspendeu e quando.
2. Conferir **Billing** (plano, cartão, faturas).
3. Antes de reativar: corrigir o path traversal e trocar os segredos (ver
   [2026-11-04-path-traversal-catchall.md](2026-11-04-path-traversal-catchall.md)).
4. Clicar em **Resume Service** e conferir se o deploy pegou o último commit
   do `master`.

## Como verificar

`curl -I https://plataforma-virtual.onrender.com/` deve responder `200`, sem o
header `x-render-routing: suspend-by-user`.
