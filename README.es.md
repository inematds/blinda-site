# 🛡️ blinda-site

**🇧🇷 [Português](README.md) · 🇺🇸 [English](README.en.md) · 🇪🇸 [Español](README.es.md)**

[![blinda-site](guia/assets/banner-es.jpg)](https://inematds.github.io/blinda-site/guia/es/)

## Qué es

blinda-site es un método para que tu agente de IA (Claude Code o Codex) proteja un sitio sin tocar el sitio que está en línea. Lee el código y la configuración del proyecto en tu máquina, encuentra lo que está expuesto y arma un plan de correcciones. Cada corrección viene con dos niveles de riesgo: qué tan grave es el problema y qué tanto puede romper algo el cambio. Antes de tocar nada, el método exige un respaldo y una simulación local. Para usarlo, basta con tener el proyecto del sitio en tu computadora y un agente abierto en su carpeta.

> **Estado (10/10/2026):** método y roadmap publicados; scripts automáticos de la v1 en construcción. El guion ya funciona con el agente haciendo las verificaciones.

## 📖 Guía de uso

Guía completa (landing + paso a paso): **https://inematds.github.io/blinda-site/guia/es/**

## Cómo usarlo hoy

1. Clona el kit: `git clone https://github.com/inematds/blinda-site ~/projetos/blinda-site`
2. Abre Claude Code o Codex en la carpeta de **tu sitio** y pide:
   > Sigue ~/projetos/blinda-site/METODO.md, etapas 1 a 3. Solo lectura: no modifiques nada fuera de la carpeta blindagem/. Guarda blindagem/relatorio.md y blindagem/plano.md.
3. Lee el informe y el plan, aprueba los ítems y pide las etapas 4 (respaldo) y 5 (simulación).
4. Con la simulación aprobada, pide las etapas 6 (aplicar) y 7 (verificar).

## Las 7 etapas

Diagnosticar → Informe → Plan → Respaldo → Simular → Aplicar → Verificar. Las tres primeras solo leen.

## Capas

| v | Capa | Qué revisa |
|---|---|---|
| 1 | Encabezados y configuración | CSP, X-Frame-Options, nosniff, Referrer-Policy, HSTS |
| 1 | Dependencias | `npm audit`, versión del framework, línea aún con soporte |
| 1 | Secretos | `.env` en git/historial, clave en el código, secreto expuesto al navegador |
| 1 | Base de datos y login | Supabase: RLS, `service_role` en el front, API sin sesión |
| 2 | Código | HTML inyectado, redirect abierto, subida de archivos sin límite |

## Archivos

- [`METODO.md`](METODO.md) (en portugués) — guion que sigue el agente
- [`ROADMAP.md`](ROADMAP.md) (en portugués) — principios, matriz de riesgo, piloto y estado
- [`modelos/`](modelos/) — modelos de informe y plan

## Licencia

MIT. Gratuito y abierto — [INEMA.CLUB](https://inema.club).
