# Método blinda-site (v0 — rascunho)

Instruções para o agente (Claude Code ou Codex) proteger um site **olhando por dentro**, no projeto local. Enquanto os scripts da v1 não ficam prontos, o agente faz as checagens à mão seguindo este roteiro.

## Regras fixas

- Trabalhar só na pasta do projeto, na máquina do dono. **Não** varrer o site no ar nem sites de terceiros.
- Etapas 1–3 não alteram nada fora de `blindagem/`.
- Antes da etapa 5, backup obrigatório.
- Correção **arriscada** só com ok explícito do dono, no momento.
- O que não deu para ver vira **"não verificado"**, nunca "ok".
- Nunca imprimir o valor de um segredo encontrado: mostrar arquivo, linha e os 4 primeiros caracteres.

## 1. Diagnosticar (só leitura)

**Camada 1 — Cabeçalhos e config**
- Achar onde o servidor define cabeçalhos: `next.config.*` (`headers()`), `vercel.json` (`headers`), `netlify.toml`, `public/_headers`, `middleware.*`.
- Conferir: `Content-Security-Policy` (pelo menos `frame-ancestors`, `object-src`, `base-uri`, `form-action`), `X-Frame-Options`, `X-Content-Type-Options: nosniff`, `Referrer-Policy`, `Strict-Transport-Security` (a Vercel já manda), `poweredByHeader: false` no Next.
- Antes de recomendar `frame-ancestors 'self'`: procurar se outro site do dono mostra este dentro de `<iframe>`.
- Antes de recomendar `form-action 'self'`: listar `<form action=...>` que enviam para fora (ex.: checkout).

**Camada 2 — Dependências**
- `npm audit --omit=dev --json` (usa só o lockfile) e resumir por gravidade.
- Versão instalada do framework (`node -p "require('next/package.json').version"`) e o último patch da mesma linha (`npm view next versions`). Se a linha parou, marcar "migração".
- Preferir o último patch da linha atual; evitar versão com menos de 1 semana.

**Camada 3 — Segredos**
- `git ls-files | grep -E '(^|/)\.env'` → `.env` rastreado = crítico.
- `git log --all --diff-filter=A --name-only -- '*.env*'` → já esteve no histórico.
- Procurar padrões de chave no código rastreado (`sk-`, `sk_live_`, `AKIA`, `eyJhbGci` de `service_role`, `-----BEGIN`).
- Variável exposta ao navegador com nome de segredo: `NEXT_PUBLIC_*SECRET*`, `NEXT_PUBLIC_*SERVICE_ROLE*`, `VITE_*KEY*` (anon key do Supabase é pública por natureza; `service_role` não).
- `.gitignore` cobre `.env*`?

**Camada 4 — Banco e login (Supabase)**
- Se existe `supabase/migrations/`: para cada `create table`, há `enable row level security` e pelo menos uma `create policy`? Tabela sem RLS = crítico.
- Sem migrations → "não verificado" + sugestão: rodar com a credencial do dono `supabase db dump --schema public` (só leitura) ou checar no painel.
- `service_role` usada em arquivo que vai para o navegador (`'use client'`, `src/` do Vite) = crítico.
- Rotas de API (`app/api/**/route.ts`, `pages/api/**`) que leem/escrevem dados sem checar sessão = alto.

## 2. Relatório

Gravar `blindagem/relatorio.md` a partir de `modelos/relatorio.md`: cada achado com camada, arquivo:linha, prova, gravidade e "não verificado" quando for o caso.

## 3. Plano

Gravar `blindagem/plano.md` a partir de `modelos/plano.md`: cada correção com o achado que resolve, risco da correção (seguro/moderado/arriscado), o que pode quebrar e como testar. Ordem pela matriz do `ROADMAP.md`. Mostrar o plano ao dono e esperar o ok.

## 4. Backup

```bash
git status --short            # sujo de outra pessoa? parar e perguntar
git switch -c blindagem/$(date +%F)
```
Se o working tree tiver mudanças que não são suas, usar `git worktree add ../<projeto>-blindagem` e trabalhar lá.

## 5. Simular

1. Aplicar os itens na branch/worktree.
2. `npm run build` e os testes que existirem.
3. Escolher porta, conferir com `ss -ltn` e **abortar se estiver ocupada**.
4. Subir `next start -p <porta>` (ou `vite preview`), ler os cabeçalhos com `fetch` em 3–4 rotas.
5. Abrir as rotas num navegador headless (Playwright) e coletar `console.error` e `pageerror`: erro de CSP aparece aqui.
6. Matar **só o PID** que você subiu (`ss -ltnp | grep :<porta>`).

## 6. Aplicar

Um item por commit, com mensagem dizendo o achado resolvido. Push só com ok do dono.

## 7. Verificar

Repetir a etapa 1 e gravar em `blindagem/relatorio.md` a tabela antes × depois.
