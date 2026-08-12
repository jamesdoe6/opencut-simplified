OpenCut — Simplified Version (stable fork)

This repository is a fork of OpenCut, the free and open-source alternative to CapCut. This version has been fixed and simplified for reliable local deployment on Windows/WSL2, after resolving several build issues encountered on both the main branch (currently undergoing a full rewrite) and on opencut-classic.
Why this fork exists

The main repository OpenCut-app/OpenCut is undergoing a complete rewrite (migration to a Rust core) and regularly breaks during build (bun run build). This fork starts from the stable v0.3.0 tag, with additional fixes for a reliable Docker build.
Prerequisites

    Git

    Bun (curl -fsSL https://bun.sh/install | bash)

    Docker Desktop (with WSL2 integration enabled if on Windows)

    A free Freesound account (for sound effects)

Installation — From scratch
1. Clone the repository

bash
git clone https://github.com/<YOUR_USERNAME>/opencut-simplified.git
cd opencut-simplified

2. Configure the environment

bash
cp apps/web/.env.example apps/web/.env.local

Edit apps/web/.env.local and fill in:
Variable	Required	Where to get it
DATABASE_URL	Yes (default value works with local Docker)	—
UPSTASH_REDIS_REST_URL / TOKEN	Yes (default value works with local Docker)	—
BETTER_AUTH_SECRET	Yes	Generate with openssl rand -base64 32
FREESOUND_CLIENT_ID / FREESOUND_API_KEY	Yes, otherwise 401 error on the Sounds panel	freesound.org/apiv2/apply
MARBLE_WORKSPACE_KEY	No, optional (blog feature only)	—
3. Start the supporting services (database + Redis) via Docker

bash
docker compose up -d db redis serverless-redis-http

    ⚠️ Do not build the app itself through Docker (plain docker compose up -d). The oven/bun:alpine image has a known bug that causes bun install to hang indefinitely on "Resolving dependencies" due to musl libc. Use Bun locally instead (see step 4).

4. Install dependencies and run the app

bash
bun install
bun dev:web

Open http://localhost:3000.
5. All-in-one command (optional)

A start:all script has been added to package.json:

bash
bun run start:all

It automatically starts Docker (db + redis) and then the Next.js dev server in a single command.
Known bugs fixed in this fork
Bug	Affected file	Fix applied
isShortcutKey not exported	apps/web/src/actions/keybindings/persistence.ts	Aliased import: isKey as isShortcutKey
isActionWithOptionalArgs not exported	Same file	See commit history for the actual function name used, confirmed via grep on apps/web/src/actions/index.ts
Docker build hangs on "Resolving dependencies"	Dockerfile (oven/bun:alpine image)	Workaround: build and run the app with Bun locally, Docker reserved for supporting services (db/redis)
401 error on the Sound Effects panel	apps/web/src/components/editor/panels/assets/views/sounds.tsx	Provide real FREESOUND_CLIENT_ID / FREESOUND_API_KEY values in .env.local
Screenshots

See the docs/screenshots/ folder:

    homepage.jpg — OpenCut landing page

    new-project.jpg — Editor interface with a new empty project

License

This project remains under the MIT License, same as the original OpenCut project. See the LICENSE file.
Credits

Based on OpenCut-app/OpenCut, created by the OpenCut community. This fork only adds build fixes and documentation to make self-hosting easier.