---
name: bilby-tech-stack
description: Use when working on web apps or related web development tasks.
---

# Tech Stack

Use this skill when working on web apps. Prefer the technologies, package boundaries, and conventions below before introducing new libraries.

## Javascript Tech Stack
- package manager: pnpm
- Monorepo: turborepo
- linter and formatter: Biome
- testing: Vitest
- UI framework: React with Next.js App Router
- Styling: Tailwind CSS with shadcn/ui
- CMS: Payload CMS
- Data fetching/client state: TanStack Query 5, tRPC React Query, and React server components. Do not introduce Zustand unless there is a strong project-level reason.
- ORM: Drizzle ORM with PostgreSQL.
- NEVER: Redux, Prisma, eslint, prettier.

example package.json scripts in `apps/[app]`:
```json
{
  "scripts": {
    "db:generate": "pnpm with-env drizzle-kit generate",
    "db:migrate": "pnpm with-env drizzle-kit migrate",
    "db:studio": "pnpm with-env drizzle-kit studio",
    "build": "pnpm with-env next build",
    "start": "pnpm with-env next start",
    "dev": "pnpm with-env next dev --turbopack",
    "typecheck": "tsc --noEmit",
    "test": "vitest run",
    "test:watch": "vitest",
    "clean": "rm -rf .next",
    "with-env": "dotenv -e ../../.env --",
    "check": "biome check .",
    "check:fix": "biome check --write ."
  }
}
```
