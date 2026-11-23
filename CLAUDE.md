# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proyecto

Carta digital interactiva + panel de administración para bares y restaurantes (demo comercial; el primer restaurante es "Tasca Malpaís", Tenerife). Mobile first: todas las pantallas están diseñadas a 390×844.

- **Fase 1 (actual):** carta y panel sin base de datos. Los datos viven en un seed JSON y localStorage detrás de la interfaz `MenuRepository`.
- **Fase 2 (más adelante):** pedidos desde la mesa y Supabase (Postgres, auth, storage, realtime). Debe poder activarse cambiando solo la implementación del repositorio, no la UI.
- La plataforma es multi-restaurante: las rutas llevan el slug del restaurante (`/[locale]/[restaurant]`).
- Idiomas de la carta: `es` (base) y `en`. Los textos de UI van en `messages/{es,en}.json`; el contenido de los platos usa campos `{ es, en }`.

## Comandos

pnpm no está instalado globalmente: se usa a través de corepack.

```bash
export COREPACK_ENABLE_DOWNLOAD_PROMPT=0
corepack pnpm install
corepack pnpm dev      # servidor de desarrollo
corepack pnpm build
corepack pnpm lint
```

Todavía no hay runner de tests configurado (se añadirá con la primera spec que lo necesite).

## Stack

Next.js 16 (App Router) · React 19 · TypeScript · Tailwind CSS v4 · `next-intl`.

## Diseño en Figma

Archivo: https://www.figma.com/design/TjiEqTKMoL17bsYFij0rTa/design (file key `TjiEqTKMoL17bsYFij0rTa`). Se lee con el MCP de Figma; antes de `get_design_context` hay que cargar la skill `figma:figma-design-to-code`.

| Página | Node ID | Uso |
|---|---|---|
| design-system | `15:5` | Fuente de tokens y componentes (lámina `15:6`, maestros `15:4057`) |
| design-menu | `1:3` | Carta pública: P1 `8:5104`, P2 `8:5362`, P3 `8:5536`, P4 `8:5812`, P5 `8:6121`, P6 `8:6289` |
| design-admin-panel | `1:4` | Panel: A1 login `11:5959` (error `11:5982`), A2 `11:6008`, A3 `11:6170`, A4 `11:6287`, A5 `11:6407`, A6 `11:6579`, A7 `11:6642`, A8 `11:6701`, A9 `11:6736` |
| wireframes-menu, wireframes-admin-panel, design-web | `0:1`, `1:2`, `110:236` | Fuera de alcance por ahora |

Tokens del design system (usarlos como variables del tema de Tailwind, nunca valores sueltos):
colores `primary #b5432a`, `secondary #4f6b3a`, `accent #e8b44a`, `bg #faf6f0`, `surface #fff`, `text #1f1b18`, `text-secondary #6b625a`, `muted #a39a91`, `border #e7dfd4`, `success #2f7d4f`, `error #c0392b`.
Tipografía: títulos en Baskervville Bold (Display 28/34, H1 22/28); resto en Inter (H2 17/24 SemiBold, Price 16/22 SemiBold, Body 15/22, Small 13/18 Medium, Caption 12/16).

## Arquitectura prevista

- `app/[locale]/[restaurant]/` carta pública; el detalle del plato y el filtro de alérgenos son hojas inferiores (bottom sheets), no rutas.
- `app/[locale]/[restaurant]/admin/{login,platos,categorias,restaurante,qr}` panel de administración.
- `lib/data/` tipos (`Restaurant`, `Category`, `Dish`), la interfaz `MenuRepository`, `local-repository.ts` (seed + localStorage) y `seed/`. **La UI solo habla con `MenuRepository`.**
- Alérgenos: los 14 de la UE, como identificadores estables, con icono y nombre traducido.
- Auth del admin en fase 1: credenciales de demo en `lib/auth/`; se sustituirá por Supabase Auth.

## Desarrollo guiado por specs

Se usan las skills de Fernando Herrera (`Klerith/fernando-skills`, instaladas en `.claude/skills/`):

1. `/spec <descripción>` genera `specs/NN-slug.md` con estado `Draft` tras hacer preguntas. No escribe código.
2. El usuario revisa y cambia el estado a `Approved` a mano. Claude nunca lo aprueba.
3. `/spec-impl NN-slug` crea la rama `spec-NN-slug` e implementa paso a paso, pausando entre pasos.

No se implementan features que no tengan spec aprobada. Cada spec debe citar los node IDs de Figma que cubre. Las specs previstas: `01-design-system`, `02-carta`, `03-admin-panel`, `04-pedidos` (fase 2).
