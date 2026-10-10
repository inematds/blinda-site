# 🛡️ blinda-site

**🇧🇷 [Português](README.md) · 🇺🇸 [English](README.en.md) · 🇪🇸 [Español](README.es.md)**

[![blinda-site](guia/assets/banner-en.jpg)](https://inematds.github.io/blinda-site/guia/en/)

## What it is

blinda-site is a method for your AI agent (Claude Code or Codex) to protect a website without touching the site that is live. It reads the project's code and settings on your machine, finds what is exposed and builds a plan of fixes. Each fix comes with two levels of risk: how serious the problem is and how likely the change is to break something. Before changing anything, the method requires a backup and a local simulation. To use it, all you need is the site project on your computer and an agent open in its folder.

> **Status (10/10/2026):** method and roadmap published; automatic v1 scripts under construction. The script already works with the agent doing the checks.

## 📖 User guide

Full guide (landing + step by step): **https://inematds.github.io/blinda-site/guia/en/**

## How to use it today

1. Clone the kit: `git clone https://github.com/inematds/blinda-site ~/projetos/blinda-site`
2. Open Claude Code or Codex in **your site's** folder and ask:
   > Follow ~/projetos/blinda-site/METODO.md (in Portuguese), steps 1 to 3. Read-only: do not change anything outside the blindagem/ folder. Write blindagem/relatorio.md and blindagem/plano.md.
3. Read the report and the plan, approve the items and ask for steps 4 (backup) and 5 (simulation).
4. With the simulation approved, ask for steps 6 (apply) and 7 (verify).

## The 7 steps

Diagnose → Report → Plan → Backup → Simulate → Apply → Verify. The first three only read.

## Layers

| v | Layer | What it looks at |
|---|---|---|
| 1 | Headers and config | CSP, X-Frame-Options, nosniff, Referrer-Policy, HSTS |
| 1 | Dependencies | `npm audit`, framework version, line still supported |
| 1 | Secrets | `.env` in git/history, key in the code, secret exposed to the browser |
| 1 | Database and login | Supabase: RLS, `service_role` on the front end, API without session |
| 2 | Code | Injected HTML, open redirect, upload without a limit |

## Files

- [`METODO.md`](METODO.md) (in Portuguese) — script the agent follows
- [`ROADMAP.md`](ROADMAP.md) (in Portuguese) — principles, risk matrix, pilot and status
- [`modelos/`](modelos/) — report and plan templates

## License

MIT. Free and open — [INEMA.CLUB](https://inema.club).
