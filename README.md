# Vincent Nasuti | Personal Cybersecurity Portfolio

A self-hosted personal portfolio showcasing my cybersecurity, information systems, and technical projects. The site is built with HTML, CSS, and JavaScript and runs on a Raspberry Pi 5 using Docker, NGINX, and Cloudflare Tunnel.

**Repository:** [vincent-nasuti-portfolio](https://github.com/vincenasuti/vincent-nasuti-portfolio)  
**GitHub profile:** [vincenasuti](https://github.com/vincenasuti)

## TL;DR

- **Frontend:** Static HTML, CSS, and JavaScript.
- **Hosting:** Raspberry Pi 5 (16 GB RAM) running Linux and Docker.
- **Web server:** NGINX in a Docker container.
- **Public access:** Cloudflare Tunnel provides an outbound connection to Cloudflare, with HTTPS for visitors.
- **Deployment:** Changes are committed to GitHub and pulled onto the Raspberry Pi.
- **Purpose:** Present my experience and document hands-on projects, their architecture, security considerations, testing, and lessons learned.

## Architecture

```text
MacBook / VS Code
       |
       | git push
       v
GitHub repository
       |
       | git pull
       v
Raspberry Pi 5 (Linux)
       |
       +-- Docker: portfolio (NGINX)
       |      +-- Static HTML / CSS / JavaScript
       |      +-- Read-only site bind mount
       |
       +-- Docker: cloudflared
              |
              | Outbound encrypted tunnel
              v
         Cloudflare edge (public HTTPS)
              |
              v
           Visitors
```

**Request flow:** Visitor → Cloudflare → Cloudflare Tunnel → `cloudflared` container → `portfolio:80` → NGINX static files.

The portfolio shares the Raspberry Pi with other homelab services, including Pi-hole and WireGuard. Those services are separate from the portfolio web application.

## Technology Stack

| Area | Technology | Role |
| --- | --- | --- |
| Frontend | HTML, CSS, JavaScript | Portfolio pages and interactions |
| Server | Raspberry Pi 5, Linux | Self-hosted infrastructure |
| Containers | Docker, Docker Compose | Service isolation and lifecycle management |
| Web server | NGINX (`nginx:stable-alpine`) | Serves static site files |
| Networking | Cloudflare Tunnel | Exposes the site without inbound web-server port forwarding |
| Transport security | HTTPS via Cloudflare | Encrypts public browser-to-edge traffic |
| Host access | SSH keys, UFW | Administrative access and host firewall controls |
| Source control | Git, GitHub | Versioning and deployment source |

## Portfolio Content

The site includes professional background, education, certifications, work experience, entrepreneurship, and project write-ups. Featured technical work includes:

- **Self-Hosted Portfolio:** Containerized NGINX website delivered through Cloudflare Tunnel.
- **Pi-hole + WireGuard:** Network-wide DNS filtering and remote VPN access.
- **Supplier Inventory Monitor:** Python-based stock-change monitoring automated with GitHub Actions and email notifications.
- **Vaultwarden:** Self-hosted password manager with Cloudflare Tunnel access and encrypted Restic backups to Google Drive using Rclone.

Project pages provide additional context on implementation, security choices, validation, troubleshooting, and future improvements.

## Repository and Deployment Layout

The live Raspberry Pi installation separates the checked-out repository from the directory NGINX serves:

```text
/home/vincenasuti/docker/portfolio/
├── repo/              # Git checkout
├── site/              # Static files served by NGINX
└── docker-compose.yml # Container configuration
```

Inside the container, the static content is mounted at `/usr/share/nginx/html` **read-only**. The portfolio container serves HTTP internally on port `80`; a local host mapping uses `127.0.0.1:8081:80`. Cloudflare Tunnel reaches the container over the shared Docker network.

> The precise files and synchronization steps between `repo/` and `site/` depend on the current deployment configuration. Check your local setup before automating updates.

## Run Locally

No framework build process is required for the static frontend.

1. Clone the repository:

   ```bash
   git clone https://github.com/vincenasuti/vincent-nasuti-portfolio.git
   cd vincent-nasuti-portfolio
   ```

2. Open `index.html` in a browser, or use a local development server:

   ```bash
   python3 -m http.server 8000
   ```

3. Visit `http://localhost:8000` to preview the site. Stop the server with `Ctrl+C`.

## Deployment Overview

The production deployment is managed on the Raspberry Pi:

1. Make and test changes locally.
2. Commit and push changes to the GitHub repository.
3. Pull the updated code on the Raspberry Pi.
4. Synchronize the appropriate static site files into the directory mounted by NGINX, if needed.
5. Confirm the container is healthy and verify the public site through Cloudflare.

Useful verification commands on the Pi:

```bash
docker ps
docker logs --tail 50 portfolio
curl -I http://127.0.0.1:8081
```

## Security Considerations

- **No direct public web-server port forwarding:** Cloudflare Tunnel establishes an outbound connection.
- **Read-only web content:** NGINX receives the static site directory as a read-only bind mount.
- **Restricted local binding:** The host-side HTTP mapping is bound to `127.0.0.1`.
- **SSH key authentication:** Used for administration of the Raspberry Pi.
- **Firewall controls:** UFW is used to manage allowed host traffic.
- **Service separation:** Portfolio, DNS filtering, VPN, and password management are operated as distinct services.

**Important:** Do not commit Cloudflare tunnel credentials, SSH private keys, `.env` secrets, password files, or backup credentials to this repository. Use sanitized configuration examples when documenting infrastructure.

## Validation and Troubleshooting

Checks used when maintaining the portfolio include:

- Confirming the NGINX container is running (`docker ps`).
- Checking the local HTTP response (`curl -I http://127.0.0.1:8081`).
- Reviewing container logs when pages fail to load.
- Verifying that the Cloudflare Tunnel can reach `portfolio:80` on the Docker network.
- Checking relative links and filename capitalization (Linux paths are case-sensitive).
- Confirming new project pages are present in the directory actually served by NGINX.

## Future Improvements

- Streamline or automate the GitHub-to-Pi deployment workflow.
- Add architecture diagrams and screenshots to project case studies.
- Expand technical documentation as new projects are completed.
- Improve automated link and availability checks.

## About Me

I'm Vincent Nasuti, an Information Systems & Analytics student at Rowan University with hands-on experience in information security and cybersecurity. I hold the CompTIA Security+ certification and use this portfolio to demonstrate practical projects, document what I learn, and share my technical work.

## License

No license is granted by default unless a license file is added to the repository. Portfolio content and project documentation are maintained by Vincent Nasuti.
