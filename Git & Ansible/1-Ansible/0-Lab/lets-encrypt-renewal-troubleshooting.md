# Let’s Encrypt Renewal Troubleshooting Guide

This README explains why your `certbot renew` run failed and how to fix it.  
It is written for Linux servers that use **Certbot** to manage free TLS certificates from **Let’s Encrypt**.

---

## 1 · Understand the log messages

| Log line | Meaning | Action |
|----------|---------|--------|
| `Certificate not yet due for renewal` | The certificate still has **more than 30 days** left (example: `mrfq.sa`, expires **12 Jun 2025**). Certbot skips it by design. | Nothing to do. |
| `Could not choose appropriate plugin … nginx` | The renewal file says to use the **nginx** plugin, but that plugin is **not installed** on this server. | Install the plugin *or* switch to another authenticator. |
| `…not appear to be installed`<br>`All renewals failed` | At least one certificate (here `zid.mrfq.sa`) could not be renewed because the required plugin is missing. | Follow the fix below. |

---

## 2 · Install the missing **nginx** plugin

Choose the command that matches *how* you installed Certbot.

### A. Certbot installed via apt/dnf

```bash
# Debian / Ubuntu
sudo apt update
sudo apt install python3-certbot-nginx   # or: sudo apt install certbot-nginx

# RHEL / CentOS / Alma / Rocky
sudo dnf install certbot-nginx
```

### B. Certbot installed via snap

```bash
# Allow plugins to run with root
sudo snap set certbot trust-plugin-with-root=ok

# Make sure the snap is up to date
sudo snap refresh certbot --classic
```

Verify that Certbot now sees the plugin:

```bash
sudo certbot plugins
# → “nginx” should appear under Authenticator / Installer
```

---

## 3 · Test a renewal (dry-run)

```bash
sudo certbot renew --dry-run --cert-name zid.mrfq.sa -v
```

- No errors? Great—real renewals will succeed automatically when the certificate is ≤30 days from expiry.
- Want to test a real renewal now? Add `--force-renewal`.

---

## 4 · (Optional) Switch the certificate to **webroot** or **standalone**

If you prefer not to install the nginx plugin you can re-issue the cert with another method:

```bash
sudo certbot certonly   --webroot -w /var/www/html   -d zid.mrfq.sa --cert-name zid.mrfq.sa
```

Certbot rewrites `/etc/letsencrypt/renewal/zid.mrfq.sa.conf` with the new **authenticator**, so future `certbot renew` runs no longer need the nginx plugin.

---

## 5 · Reload Nginx automatically after renewal

The nginx plugin reloads Nginx for you.  
If you switched to webroot/standalone, add a *deploy hook*:

```bash
sudo mkdir -p /etc/letsencrypt/renewal-hooks/deploy
sudo nano /etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh
```

```bash
#!/bin/bash
systemctl reload nginx
```

Make it executable:

```bash
sudo chmod +x /etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh
```

---

✅ Now your Let’s Encrypt certs should renew cleanly and reload Nginx automatically.
