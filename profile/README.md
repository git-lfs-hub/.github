# Git LFS Hub

**Git LFS Hub** is a self-hosted **[Git LFS](https://git-lfs.com/)** stack for teams that want to keep large files in Git without paying the premium or standing up a separate object store, auth layer, and ops story by hand. You bring a **[Cloudflare](https://www.cloudflare.com/)** account and a **GitHub** org or repo; you get an LFS endpoint your teammates can `git lfs push` and `git lfs pull` against, gated by GitHub OAuth, backed by **[R2](https://developers.cloudflare.com/r2/)** for blob storage, with **Durable Objects** powering **[LFS file locking](https://github.com/git-lfs/git-lfs/wiki/File-Locking)** for workflows that need it. A bundled documentation site walks your users through installing Git LFS, pointing their remotes at your server, and the day-to-day push/pull flow.

## What you get

- A **Cloudflare Worker** speaking the standard Git LFS batch API, deployable in one command.
- **GitHub OAuth** sign-in so access to your LFS objects mirrors access to the underlying GitHub repos — no separate user database.
- **R2 storage** for the actual blobs, billed at R2 rates with zero egress fees.
- **File locks** via Durable Objects, opt-in per workflow.
- A **docs site** served from the same Worker, so onboarding new contributors is a single URL.
- All of it backed by a **solid test suite**, including end-to-end tests that exercise a real `git lfs push` against a live staging Worker — so the version you deploy is the version that was proven to work.

## The flow

1. Clone **deploy**, the monorepo that ties everything together.
2. Edit `vars.input.json` with your Cloudflare account, R2 bucket name, GitHub org, and OAuth app details.
3. Run `bun run config` — the **config** renderer merges your inputs with defaults, validates against a JSON Schema, and writes out `wrangler.jsonc` and `github-app.md` (instructions for creating the GitHub App).
4. Run `bun run deploy` — **docs** is built into the Worker's static assets, **server** is bundled and shipped to Cloudflare.
5. Point your team's `.lfsconfig` at your new endpoint, sign in once via GitHub, and `git lfs push` works.

## The repositories

Each repo is independently useful, but **deploy** is the entry point — it consumes the other four as workspaces or submodules.

- **[deploy](https://github.com/git-lfs-hub/deploy)** — the Turbo monorepo and your starting point. Holds `vars.input.json`, the deploy scripts, the GitHub Actions workflows (`pr.yml`, `main.yml`), and submodules for `server/`, `docs/`, and `e2e/`. Clone this first.
- **[server](https://github.com/git-lfs-hub/server)** — the Worker source: a Hono app implementing the LFS batch API, OAuth callback, R2 presigning, and Durable Object lock store. Use this repo directly if you want to embed the Worker into a different deploy story and manage Wrangler config yourself.
- **[docs](https://github.com/git-lfs-hub/docs)** — the end-user documentation site, built with DocMD. Covers installing Git LFS, configuring remotes, and using your LFS Hub instance. Built into `server/public/` as part of the deploy pipeline; edit it in isolation if you just want to tweak guides.
- **[config](https://github.com/git-lfs-hub/config)** — the vars renderer, invoked as `bunx github:git-lfs-hub/config` (or `bun run config` from a deploy checkout). Merges `vars.input.json` with package defaults, Ajv-validates against the schema, and renders the Handlebars templates `wrangler.template.jsonc` and `github-app.template.md` into their final forms. Idempotent on re-run.
- **[e2e](https://github.com/git-lfs-hub/e2e)** — the end-to-end test harness that proves each release works before it ships. A reusable GitHub Actions workflow (`staging.yml`) plus a vitest suite (`test-docs`, `test-git-lfs`) deploys a throwaway `-staging` Worker and exercises a real `git lfs push` against it on every change to the upstream project.

## Support

File bug reports or feature requests in the repo that matches the area — **deploy** for setup, vars, or CI wiring; **server** for API or Worker behavior; **docs** for written guides; **config** for vars schema or template rendering; **e2e** for the staging workflow or smoke tests. If you are unsure, open it on **deploy** and maintainers will redirect.
