# Roadmap — blinda-site

> Estado em 10/10/2026: **método e roadmap publicados; scripts da v1 em construção.** Nada aqui ainda roda sozinho: hoje o método é seguido pelo agente (Claude Code ou Codex) lendo o `METODO.md`.

## Por que existe

Em 10/10/2026 um scanner externo deu nota D (46/100) ao inema.club. As correções foram pequenas, mas o caso mostrou três coisas:

- O problema mais sério nem estava no site testado: **pay.inema.pro** (o checkout) não enviava nenhum cabeçalho de segurança.
- Varrer o site de fora tem limite (lê 1 página) e, em site de terceiros, pode parecer invasão.
- Corrigir sem método quebra coisas: a CSP "completa" teria tirado o cache do portal.

O blinda-site faz o caminho inverso: **olha por dentro, só no projeto local**, diagnostica, planeja, faz backup, simula e só então aplica.

## Princípios

1. **Só local, só leitura no diagnóstico.** O kit lê código, config, lockfile e histórico do git na máquina do dono. Não faz requisição ao site no ar, a menos que o dono peça e seja o site dele.
2. **Backup antes de tudo.** Nenhuma alteração sem um estado limpo salvo (commit + branch `blindagem/<data>`, ou worktree).
3. **Simular antes de aplicar.** Build + servidor local + leitura dos cabeçalhos + navegador vendo o console.
4. **Dois eixos de risco.** A gravidade do achado e o risco da correção são medidos separadamente.
5. **Dizer "não verificado" em vez de "tudo certo".** Se o kit não consegue ver algo (ex.: tabela criada no painel do Supabase), o relatório diz isso.
6. **Arriscado sempre pergunta.** Correção marcada como arriscada só é aplicada com o ok do dono no momento.

## As 7 etapas

| # | Etapa | O que faz | Altera o projeto? |
|---|---|---|---|
| 1 | Diagnosticar | Roda as checagens das camadas | Não |
| 2 | Relatório | `blindagem/relatorio.md`: achado, prova, gravidade | Só cria a pasta `blindagem/` |
| 3 | Plano | `blindagem/plano.md`: correção, risco de aplicar, ordem | Só a pasta `blindagem/` |
| 4 | Backup | commit do estado limpo + branch `blindagem/<data>` (ou `git worktree`) | Git apenas |
| 5 | Simular | aplica numa cópia/branch, build, sobe em porta livre, confere cabeçalhos e console | Na cópia |
| 6 | Aplicar | só os itens aprovados, um por vez, com commit cada | Sim |
| 7 | Verificar | repete o diagnóstico e grava o antes × depois | Só `blindagem/` |

## Matriz de risco

**Gravidade do achado:** crítico · alto · médio · baixo.
**Risco da correção:** seguro · moderado · arriscado.

| | Seguro | Moderado | Arriscado |
|---|---|---|---|
| **Crítico** | faz primeiro | faz, testando | pergunta antes |
| **Alto / Médio** | faz | faz, testando | pergunta antes |
| **Baixo** | faz se for barato | anota | anota |

Exemplos reais (piloto 10/10/2026):

| Achado | Gravidade | Correção | Risco da correção |
|---|---|---|---|
| Checkout sem nenhum cabeçalho de segurança | alto | cabeçalhos + CSP mínima | seguro |
| Next.js 16.1.6 na faixa de alertas | médio | patch 16.3.8 | seguro |
| Next.js 14 sem novas correções | alto | migrar 14 → 16 | arriscado |
| Sem CSP | médio | CSP mínima (frame-ancestors, object-src, base-uri, form-action) | seguro |
| Sem CSP de scripts | médio | `script-src` com nonce | arriscado (tira o cache estático) |
| Chave secreta no git | crítico | trocar a chave | moderado |
| Chave secreta no histórico | crítico | reescrever o histórico | arriscado |

## Camadas

### v1
1. **Cabeçalhos e config** — `next.config.*`, `vercel.json`, `netlify.toml`, `_headers`, middleware. Confere CSP, X-Frame-Options, nosniff, Referrer-Policy, HSTS, `poweredByHeader`.
2. **Dependências** — `npm audit` sobre o lockfile, versão do framework e se a linha ainda recebe correções.
3. **Segredos** — `.env*` rastreado ou no histórico do git, padrões de chave no código, variável secreta exposta ao navegador (`NEXT_PUBLIC_*`, `VITE_*` com nome de segredo), `.gitignore`.
4. **Banco e login (Supabase)** — tabelas sem RLS nas migrations de `supabase/`, `service_role` no código do front, rotas de API sem checagem de sessão.
   - Sem migrations locais → **"não verificado"**.
   - Opcional: consulta só leitura pelo Supabase CLI com a credencial do próprio dono.

### v2
5. **Código** — o agente lê e aponta: `dangerouslySetInnerHTML`, redirect aberto, upload sem limite, `eval`. Sem nota automática no início.
- Modo CI (roda no GitHub Actions a cada push, só diagnóstico).

### v3
- Painel com o histórico das notas de cada site.

## Piloto (sites do INEMA)

| Ordem | Site | Repo | Por quê |
|---|---|---|---|
| 1 | pay.inema.pro | inemapro-mono/apps/pay | checkout; estava sem cabeçalhos |
| 2 | inema.club | portal | maior tráfego; já corrigido no dia 10/10 (serve de referência) |
| 3 | inema.vip | INEMAVIPw/inema-vip-landing | estático (Vite) — testa o caminho sem Next |
| 4 | eventos.inema.pro | inemaeventos | muitas páginas e scripts |

## Status

| Item | Estado |
|---|---|
| Método (`METODO.md`) | publicado (rascunho v0) |
| Roadmap | publicado |
| Correções manuais de 10/10 (portal, pro, pay, vip) | feitas |
| Scripts camada 1–4 | a fazer |
| Modelos de relatório e plano | rascunho em `modelos/` |
| Skill (`SKILL.md`) | a fazer, depois dos scripts |
| Piloto pelo kit | a fazer |
