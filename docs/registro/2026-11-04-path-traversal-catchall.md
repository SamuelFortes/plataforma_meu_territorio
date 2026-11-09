# Path traversal na rota catch-all expõe arquivos do servidor

- **Data:** 2026-11-04 (falha identificada no review de 2026-10-22)
- **Tipo:** segurança
- **Status:** em andamento
- **Responsável:** Allyson Kawã
- **PR / commits:** https://github.com/SamuelFortes/plataforma_meu_territorio/pull/13

## Problema

Qualquer pessoa, sem login, conseguia baixar arquivos do servidor pela URL.
Por exemplo, `/..%2F..%2F.env` devolvia o `.env` (credenciais do banco, segredo
do JWT, client secret do Google) e `/..%2F..%2Fbackend%2Fmain.py` devolvia o
código-fonte. A falha esteve no ar enquanto o site funcionava.

## Causa

A rota `/{catchall:path}` em `backend/main.py`, que serve o build do React,
montava o caminho com `f"../frontend/dist/{catchall}"` e servia o arquivo se
ele existisse. Como o `catchall` pode conter `../` codificado (`%2F`), o caminho
saía de `frontend/dist` e chegava em qualquer arquivo que o processo pudesse
ler.

## Solução

O caminho agora é resolvido com `os.path.realpath` e só é servido se continuar
dentro de `frontend/dist` (`os.path.commonpath`). No Windows, um caminho em
outro drive (`D:/...`) faz o `commonpath` lançar `ValueError`, tratado como
"fora do dist". Em qualquer outro caso a rota devolve o `index.html`, como
antes, e o React Router cuida da rota.

A primeira versão da correção foi feita sobre um `master` local desatualizado
(antes da mudança para `backend/`) e foi refeita sobre o `master` atual.

## Como verificar

Com o build em `frontend/dist`, rodando de dentro de `backend/`:

- `/..%2F..%2F.env`, `..%5C`, `%2E%2E%2F`, `C:%5CWindows%5Cwin.ini`,
  `%2Fetc%2Fpasswd` → devolvem o `index.html`.
- `/`, `/login` → `index.html`; `/logo.jpeg` → `image/jpeg`;
  `/api/rota-inexistente` → 404 em JSON.

Antes da correção, os dois primeiros ataques devolviam o `.env` e o
`backend/main.py`.

Em produção (https://meu-territorio.onrender.com, 2026-11-09, antes do merge),
as variações com `%2F` e `../` foram barradas com `400` pela borda do Render, que
não é o nosso código. A correção continua necessária: não dá para depender de um
filtro externo que pode mudar.

## Pendências

- Fazer o merge do PR.
- Trocar no Render, antes de reativar o serviço: `JWT_SECRET` (desloga todos
  os usuários), senha do banco e client secret do Google OAuth. Considerar
  vazados todos os segredos que estavam no `.env` de produção.
