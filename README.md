# Révisions EDN (CAP EDN)

Appli de révision EDN, publiée sur https://revisions-edn.nicolas-segond.workers.dev/

- `worker.js` : tout le site (page + API de synchro `/api/state`)
- `wrangler.toml` : réglages Cloudflare (stockage KV `DATA`)
- Chaque envoi sur la branche `main` est publié automatiquement par Cloudflare (Workers Builds).
