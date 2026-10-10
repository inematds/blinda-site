# 🛡️ blinda-site

**🇧🇷 [Português](README.md) · 🇺🇸 [English](README.en.md) · 🇪🇸 [Español](README.es.md)**

[![blinda-site](guia/assets/banner.jpg)](https://inematds.github.io/blinda-site/guia/)

## O que é

O blinda-site é um método para o seu agente de IA (Claude Code ou Codex) proteger um site sem tocar no site que está no ar. Ele lê o código e as configurações do projeto na sua máquina, encontra o que está exposto e monta um plano de correções. Cada correção vem com dois níveis de risco: o quanto o problema é grave e o quanto a mudança pode quebrar algo. Antes de mexer, o método exige backup e uma simulação local. Para usar, basta ter o projeto do site no computador e um agente aberto na pasta dele.

> **Estado (10/10/2026):** método e roadmap publicados; scripts automáticos da v1 em construção. O roteiro já funciona com o agente fazendo as checagens.

## 📖 Guia de uso

Guia completo (landing + passo a passo): **https://inematds.github.io/blinda-site/guia/**

## Como usar hoje

1. Clone o kit: `git clone https://github.com/inematds/blinda-site ~/projetos/blinda-site`
2. Abra o Claude Code ou o Codex na pasta do **seu site** e peça:
   > Siga ~/projetos/blinda-site/METODO.md, etapas 1 a 3. Só leitura: não altere nada fora da pasta blindagem/. Grave blindagem/relatorio.md e blindagem/plano.md.
3. Leia o relatório e o plano, aprove os itens e peça as etapas 4 (backup) e 5 (simulação).
4. Com a simulação aprovada, peça as etapas 6 (aplicar) e 7 (verificar).

## As 7 etapas

Diagnosticar → Relatório → Plano → Backup → Simular → Aplicar → Verificar. As três primeiras só leem.

## Camadas

| v | Camada | O que olha |
|---|---|---|
| 1 | Cabeçalhos e config | CSP, X-Frame-Options, nosniff, Referrer-Policy, HSTS |
| 1 | Dependências | `npm audit`, versão do framework, linha ainda suportada |
| 1 | Segredos | `.env` no git/histórico, chave no código, segredo exposto ao navegador |
| 1 | Banco e login | Supabase: RLS, `service_role` no front, API sem sessão |
| 2 | Código | HTML injetado, redirect aberto, upload sem limite |

## Arquivos

- [`METODO.md`](METODO.md) — roteiro que o agente segue
- [`ROADMAP.md`](ROADMAP.md) — princípios, matriz de risco, piloto e status
- [`modelos/`](modelos/) — modelos de relatório e plano

## Licença

MIT. Gratuito e aberto — [INEMA.CLUB](https://inema.club).
