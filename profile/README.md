# Git LFS Hub

**Git LFS Hub** is a self-hosted **[Git LFS](https://git-lfs.com/)** stack aimed at teams that want large files in Git without running a separate object store and auth story by hand. You deploy a **[Cloudflare Worker](https://developers.cloudflare.com/workers/)** that speaks the standard Git LFS API, store blobs in **[Cloudflare R2](https://developers.cloudflare.com/r2/)**, sign users in with **GitHub OAuth**, and use **Durable Objects** for **[LFS file locking](https://github.com/git-lfs/git-lfs/wiki/File-Locking)** when your workflow needs it. A documentation site helps everyone install Git LFS, point remotes at your server, and day-to-day push and pull.

If you are evaluating or adopting the project, start with **[git-lfs-hub/deploy](https://github.com/git-lfs-hub/deploy)**—that is where setup, configuration, and deploy commands live.

## Repositories

| Repository | Description |
|------------|-------------|
| [**deploy**](https://github.com/git-lfs-hub/deploy) | Main monorepo (Turbo): renders deployment config, builds the Worker and docs, and ties everything together. **Clone this first** if you want to run or ship the full product locally or to Cloudflare. |
| [**server**](https://github.com/git-lfs-hub/server) | The LFS server itself—a Hono app on Workers with the LFS batch API, OAuth, R2, and locks. Use this repo if you only need the Worker source and already manage Wrangler config and static assets yourself. |
| [**docs**](https://github.com/git-lfs-hub/docs) | End-user documentation (DocMD): Git, GitHub, and using your LFS Hub instance. It is usually built and served as part of **deploy**; use this repo if you want to edit or read the docs in isolation. |

## Support

Report bugs, ask for features, or open pull requests in the repository that matches the change (**deploy** for full-stack or config flow, **server** for API or Worker behavior, **docs** for written guides). If you are unsure, open an issue on **deploy** and maintainers can redirect.
