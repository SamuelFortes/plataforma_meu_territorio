# Banco de dados (Supabase) fora do ar: login e telas com dados dão erro 500

- **Data:** 2026-11-23
- **Tipo:** incidente
- **Status:** aberto
- **Responsável:** Allyson Kawã
- **PR / commits:** nenhum (a correção é no painel do Supabase)

## Problema

Ao entrar com Google, o callback `/api/auth/google/callback` devolvia
`{"detail":"Erro interno no servidor."}`. O site abre, mas toda rota que usa o
banco dá 500 (ex.: `/api/cargos`, que é pública).

## Causa

O banco não aceita conexões. O `/health` de produção mostrava o erro do pooler
do Supabase (porta 6543): `FATAL: (ENOTFOUND) tenant/user postgres.<projeto> not found`.
Isso indica que o projeto do Supabase foi pausado (o plano grátis pausa projetos
depois de alguns dias sem uso, e o site ficou parado durante a suspensão no
Render) ou foi apagado. Qual dos dois aconteceu: não confirmado.

## Solução

Reativar o projeto em supabase.com/dashboard (botão **Restore project**). Se o
projeto tiver sido apagado: criar outro banco, rodar `alembic upgrade head` e
trocar a `DATABASE_URL` no Render (os dados antigos só voltam se houver backup).

## Como verificar

`https://meu-territorio.onrender.com/health` deve responder
`{"status":"ok","database":"connected"}`, e o login com Google deve funcionar.

## Pendências

- Reativar o projeto no Supabase (precisa da conta dona do projeto).
- Evitar nova pausa por inatividade (plano pago ou uso periódico do banco).
