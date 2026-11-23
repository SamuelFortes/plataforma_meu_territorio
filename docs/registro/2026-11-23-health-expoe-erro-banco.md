# Rota pública /health expunha detalhes do banco de dados

- **Data:** 2026-11-23
- **Tipo:** segurança
- **Status:** em andamento
- **Responsável:** Allyson Kawã
- **PR / commits:** branch `fix/health-vaza-erro-banco`

## Problema

Durante o incidente do banco fora do ar
([registro](2026-11-23-banco-supabase-fora.md)), a rota `/health`, que é pública
e não exige login, devolvia a mensagem crua da exceção do banco, com IP do
servidor, porta e usuário do Postgres.

## Causa

`backend/main.py`, função `health_check`: o `except` retornava `str(e)` no corpo
da resposta.

## Solução

O detalhe do erro vai só para o log do servidor (`logger.exception`) e a
resposta passa a ser `{"status":"error","database":"unavailable"}`. O status
HTTP continua 200 para não mudar o comportamento de quem já consulta a rota.

## Como verificar

Com um `DATABASE_URL` inacessível, `GET /health` responde
`{"status":"error","database":"unavailable"}` e o erro completo aparece no log.
Testado localmente com `TestClient` em 2026-11-23.

## Pendências

- Merge e deploy no Render.
