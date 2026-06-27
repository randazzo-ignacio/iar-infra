# static-page role

Deploys the emacboros landing page to daftpunk (i.ar). Caddy on daftpunk
serves the files directly via `file_server` — no separate HTTP server
process needed.

## Files

| File | Purpose |
|------|---------|
| `files/index.html` | Landing page markup |
| `files/style.css` | Matrix/terminal aesthetic stylesheet |
| `files/script.js` | Boot sequence, matrix rain, scroll reveal |
| `tasks/main.yml` | Creates www-data user, web root, deploys files |

## Deployment

The role is included in `playbooks/site.yml` and runs on `daftpunk` when
`tool_static_page_enabled: true` (set in `host_vars/daftpunk.yml`).

Caddy runs on every cloud host but each host's Caddyfile only defines
the domains that host actually serves. daftpunk's Caddyfile serves
`i.ar` from `{{ static_page_root }}` via `file_server`.

## Architecture

```
[Client] → HTTPS → [Caddy @ daftpunk] → file_server → /var/www/emacboros/
                   i.ar:443                             index.html, style.css, script.js
```

Caddy handles TLS automatically via Let's Encrypt. No reverse proxy
needed — static files are served directly from disk.