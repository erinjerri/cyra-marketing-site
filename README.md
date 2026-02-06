# cyra-marketing-site — PM + Engineering Handoff

This repository contains a Next.js + Payload CMS site backed by MongoDB. The goal is to support multiple websites with a shared UI/component system and a single content model, while allowing per-site pages and blocks to be tracked and executed via a master Notion checklist.

This README is a handoff for senior/junior engineers and a PM checklist anchor.

---

**Contents**
1. Project Overview
2. Quick Start
3. Environment Variables
4. Content Model Summary
5. Notion Master Sheet (Schema + Starter Checklist)
6. Figma → Aura → Payload Workflow
7. Implementation Patterns
8. Deployment (Two Options)
9. Troubleshooting

---

## 1) Project Overview

**Purpose**
- Build and ship multiple sites from a shared codebase and shared components.
- Maintain a single source of truth for content (Payload CMS).
- Track all UI blocks, components, and content sources in a master checklist.

**Architecture**
- Frontend: Next.js (App Router)
- Backend: Payload CMS
- Database: MongoDB
- Hosting: Netlify (frontend), Payload can be separate or integrated

---

## 2) Quick Start

**Requirements**
- Node.js: `^18.20.2 || >=20.9.0`
- pnpm: `^9 || ^10`

**Commands (run in `/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite`)**
```bash
pnpm install
pnpm dev
```

Other commands:
```bash
pnpm build
pnpm start
pnpm lint
pnpm test
pnpm generate:types
pnpm generate:importmap
```

---

## 3) Environment Variables

Defined in `/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/.env.example`:

- `PAYLOAD_SECRET`  
  Required. JWT + encryption secret for Payload.

- `DATABASE_URL`  
  MongoDB connection string.

- `NEXT_PUBLIC_SERVER_URL`  
  Public URL for links, CORS, previews (no trailing slash).

- `CRON_SECRET`  
  Optional. Used for scheduled Payload jobs.

- `PREVIEW_SECRET`  
  Used for preview validation.

**Local vs Production**
- Local values can be simple; production values should be strong and stored in hosting secrets.

---

## 4) Content Model Summary

**Collections**
- Pages
- Posts
- Media
- Categories
- Users
- Subscribers
- Redirects (plugin)
- Forms + Form Submissions (plugin)
- Search Results (plugin)

**Globals**
- Header
- Footer
- Aura Home

**Frontend Flow**
- `Pages` and `Posts` are rendered by their templates.
- `Aura Home` is a global with its own UI and content.
- Media is shared across all content types via Upload fields.

Key files:
- Aura home UI: `/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/app/(frontend)/AuraHome.tsx`
- Aura home data: `/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/globals/AuraHome.ts`
- Pages template: `/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/app/(frontend)/[slug]/page.tsx`
- Posts template: `/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/app/(frontend)/posts`

---

## 5) Notion Master Sheet (Schema + Starter Checklist)

**Goal**
One master sheet for every block on every page across all sites. This is the PM “source of truth” for what exists, what’s reusable, and what’s unique per site.

### Core Delivery Set (column definitions)
- **Site**: Which site this row belongs to.
- **Section**: High-level group (e.g., Home, About, Blog).
- **Block**: UI block name (e.g., Hero, CTA, Footer).
- **Page/Route**: URL path.
- **Components**: Component(s) used.
- **Content Type**: Page / Global / Collection / Form / Archive.
- **Primary Collection**: Where the data lives (Pages, Posts, Globals, etc.).
- **References**: Media / Categories / Forms / Relationships.
- **Owner**: Accountable person.
- **Priority**: P0 / P1 / P2.
- **Effort**: S / M / L or hours.
- **Dependency**: What must exist first.
- **Implementation Path**: File path(s).
- **Design Link**: Figma frame.
- **Source of Truth**: Pages / Posts / Globals / Forms.
- **Last Updated**: Date.
- **Status**: Concept / In Progress / Done.
- **Done**: Checkbox.

### Recommended Views
- **By Site** (filter `Site`)
- **By Route** (group by `Page/Route`)
- **By Block** (group by `Block`)
- **By Status** (kanban)
- **By Owner** (workload)

### Starter Checklist (seed rows)
Use this to initialize the master database, then duplicate per site. Replace `SITE_A`, `SITE_B`, `SITE_C` with actual site names.

```csv
Site,Section,Block,Page/Route,Components,Content Type,Primary Collection,References,Owner,Priority,Effort,Dependency,Implementation Path,Design Link,Source of Truth,Last Updated,Status,Done
SITE_A,Global,Header,/global,Header,Global,Header,Nav Links,,P0,M,Pages/Posts,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/Header/Component.tsx,,Globals,,Concept,false
SITE_A,Home,Hero,/,AuraHero,Global,Globals (aura-home),Media/Posts/Pages,,P0,M,Media,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/app/(frontend)/AuraHome.tsx,,Globals,,Concept,false
SITE_A,Home,Experience Cards,/,Cards,Global,Globals (aura-home),Pages/Posts,,P1,M,Pages/Posts,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/app/(frontend)/AuraHome.tsx,,Globals,,Concept,false
SITE_A,Home,Read,/read,Archive,Collection,Posts,Categories/Media,,P1,M,Posts,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/app/(frontend)/posts/page.tsx,,Posts,,Concept,false
SITE_A,Home,Watch,/watch,Video Grid,Page,Pages,Media,,P1,M,Pages,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/app/(frontend)/AuraHome.tsx,,Pages,,Concept,false
SITE_A,Home,Buy,/buy,CTA,Page,Pages,Media,,P1,S,Pages,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/app/(frontend)/AuraHome.tsx,,Pages,,Concept,false
SITE_A,Global,Footer,/global,Footer,Global,Footer,Nav Links,,P0,S,Pages/Posts,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/Footer/Component.tsx,,Globals,,Concept,false
SITE_A,Admin,Pages,/admin,Payload Admin,Collection,Pages,,,"P0",S,,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/collections/Pages/index.ts,,Pages,,Concept,false
SITE_A,Admin,Posts,/admin,Payload Admin,Collection,Posts,Categories/Media,,P0,S,,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/collections/Posts/index.ts,,Posts,,Concept,false
SITE_A,Admin,Media,/admin,Payload Admin,Collection,Media,Used everywhere,,P0,S,,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/collections/Media.ts,,Media,,Concept,false
SITE_A,Admin,Forms,/admin,Payload Admin,Collection,Forms,Form Submissions,,P1,S,,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/plugins/index.ts,,Forms,,Concept,false
SITE_A,Admin,Redirects,/admin,Payload Admin,Collection,Redirects,Pages/Posts,,P1,S,,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/plugins/index.ts,,Redirects,,Concept,false
SITE_A,Admin,Search,/admin,Payload Admin,Collection,Search Results,Posts,,P1,S,,/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/plugins/index.ts,,Search,,Concept,false
SITE_B,Home,Hero,/,Hero,Page,Pages,Media,,P0,M,Media,,,Pages,,Concept,false
SITE_C,Home,Hero,/,Hero,Page,Pages,Media,,P0,M,Media,,,Pages,,Concept,false
```

---

## 6) Figma → Aura → Payload Workflow

**Rule**
- Each Figma frame maps to one Notion row.
- If a block is reused across sites, it gets one row with `Scope=Shared` and is referenced by each site.

**Flow**
1. Figma wireframe + Aura build establishes UI.
2. Add a Notion row for each block and link the Figma frame.
3. Decide if block is reusable or unique.
4. Build reusable blocks once; only create unique blocks per site.

---

## 7) Implementation Patterns

**Where to add blocks/components**
- Components: `/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/components`
- Blocks: `/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/blocks`
- Globals: `/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/globals`

**Data flow**
- Media is the shared asset store.
- Posts and Pages are core content types.
- Aura Home pulls from the `aura-home` Global and can reference Pages/Posts.

---

## 8) Deployment (Two Options)

### Option A: Netlify Frontend + Payload Separate
- Deploy Next.js frontend to Netlify.
- Deploy Payload API/admin to a Node host (Render/Fly/EC2/etc.).
- Ensure `NEXT_PUBLIC_SERVER_URL` points to frontend.
- Configure CORS in Payload to allow frontend domain.

### Option B: All-in-one Next.js + Payload
- Deploy as a single Node service.
- Ensure MongoDB networking is whitelisted.
- Simplifies preview and admin but reduces separation of concerns.

**MongoDB basics**
- Use a dedicated database per environment (dev/staging/prod).
- Use a user with least privilege.

**Post-deploy checks**
- Admin loads and creates Pages/Posts.
- Media uploads and renders in frontend.
- Redirects and search results populate.

---

## 9) Troubleshooting

**Payload admin errors**
- `useServerFunctions must be used within a ServerFunctionsProvider`  
  Usually indicates missing or broken Payload Admin layout or import map. Verify:
  `/Users/erinjerri/Documents/GH-Repos-Main/cyra-marketing-site/cyra-timecake-msite/src/app/(payload)/layout.tsx`

**Media URLs fail**
- Check `NEXT_PUBLIC_SERVER_URL`
- Verify CORS settings in Payload config

**Import map errors**
- Run `pnpm generate:importmap`

---

## Notes for Handoff

- This README is the master operational doc.
- The Notion master sheet is the execution system of record.
- Always tie UI blocks back to a Figma frame and a Notion row.
