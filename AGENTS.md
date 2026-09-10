# AGENTS.md

Repo: `2Kappa-Mikey/x-ui-kappa` (fork of `mozaroc/x-ui-kappa`, which is a fork of `GFW4Fun/x-ui-kappa`). Bash installer scripts for **3x-ui + nginx** exposing all VPN inbounds through a single port **443** via SNI routing. Not a codebase — no source tree, build system, or test suite. Linux-only (Debian 12/Ubuntu 24). README is in Russian.

## Layout & purpose

- `x-ui-kappa.sh` — main installer (flags: `-install yes -panel 1 -subdomain <dom> -reality_domain <dom> -ONLY_CF_IP_ALLOW <y|n> -websub 0|1 -clash 0..3 -uninstall yes`). Downloads the **latest** 3x-ui release (tag fetched from GitHub API, used in the download URL). This was the v2.9.4-pin bug fixed in this fork.
- `x-ui-latest.sh` — near-identical installer (small diff) that also fetches the **latest** 3x-ui release tag.
- `backup.sh` — interactive backup/restore of panel + nginx configs (asks for path, menu-driven).
- `randomfakehtml.sh` — installs a random fake HTML site into `/var/www/html/` (from `GFW4Fun/randomfakehtml`).
- `sub-3x-ui.html`, `sub-3x-ui-classical.html` — web subscription pages (`-websub 1` = classical).
- `clash/*.yaml` — Clash meta configs selected by `-clash 0..3`.
- `repare-after-286-update.sh` — post-upgrade repair (rewrites nginx panel locations via awk).
- `changelog` — version notes (v0.1–v0.3).
- `media/` — README screenshots only.

## Architecture (how the single-443 scheme works)

Clients only ever see port 443; everything else is internal:

- nginx `stream` block with `ssl_preread` (`/etc/nginx/stream-enabled/stream.conf`) routes by SNI:
  - `reality_domain` → `127.0.0.1:8443` (Xray VLESS+Reality inbound)
  - main `domain` → `127.0.0.1:7443` (nginx HTTPS server: panel, WS/gRPC/XHTTP, subscriptions, fake site)
  - default → 8443 (Reality fallback/camouflage)
- Reality inbound `target: 127.0.0.1:9443` — the fake site with a real cert (another nginx vhost).
- Each inbound uses `externalProxy: {dest: <main-domain>, port: 443}`; real inbounds listen on random internal ports (8443/ws/grpc/trojan) plus `/dev/shm/uds2023.sock` for XHTTP (packet-up mode). nginx maps URL path `/port/path` → internal port.
- Panel and sub service listen on random high ports, proxied by nginx; panel is at `https://<domain>/<random-10-char-path>/`.
- ufw: only 22/80/443 allowed; BBR sysctl appended to `/etc/sysctl.conf`.

## Operational gotchas (agent will otherwise guess wrong)

- Scripts run as root, stop nginx and `fuser -k 80/tcp 443/tcp` before `certbot certonly --standalone`. **Not idempotent-safe to re-run casually**: it deletes `/usr/local/x-ui`, `/etc/x-ui`, nginx sites, then rebuilds DB via sqlite3 (`UPDATE_XUIDB`).
- Requires two domains whose A-records point at the server; certbot must issue certs for both or the script exits.
- Supply chain risk: downloads 3x-ui/xray/sub2sing-box/fake-site archives from GitHub **without SHA checksum verification**; sub2sing-box binary comes from `legiz-ru/sub2sing-box` v0.0.9.
- Panel login is printed to console at end of install (log + sqlite `settings` table); panel listens on `127.0.0.1:<random>` behind nginx by default after install.
- `changelog` documents flags: `-clash 3` (re-filter ECH rules), `-websub 1` (classical page), `-uninstall yes`, `-ONLY_CF_IP_ALLOW no`.

## Fork-specific facts

- Remote origin: `https://github.com/2Kappa-Mikey/x-ui-kappa.git` (currently private).
- README install/uninstall/backup commands point to this fork (`2Kappa-Mikey/x-ui-kappa`) — they will 404 until the repo is made public.
- Deliberately NOT repointed to the fork (separate upstream repos, leave alone):
  - `mozaroc/3x-ui-kappa` → fake-site templates (`x-ui-kappa.sh`, `x-ui-latest.sh`).
  - `legiz-ru/x-ui-kappa` → sub-page HTML + clash configs (`x-ui-kappa.sh`, `x-ui-latest.sh`).
- Git is at `C:\Program Files\Git\cmd\git.exe` (add to PATH; not on default PATH). No WSL distro installed, so **`bash -n` cannot run locally** — verify bash edits by visual comparison against the matching lines in `x-ui-latest.sh`.
- git config already set locally (`Kappa Mikey` / `2firewalker@gmail.com`); commit only when asked.

## Verification flow (real server, not local)

No tests exist. Verify by SSH to the target VPS and check:
- `systemctl is-active x-ui nginx ssh`
- `ss -tlnp` shows nginx on 443, xray on internal ports bound to `127.0.0.1`
- `curl -sk https://<domain>/<panel-path>/` → 200
- `nginx -t`, `certbot certificates`, `ufw status verbose`