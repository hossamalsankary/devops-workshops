# Let's Encrypt TLS Certificate Setup & Renewal (Certbot)

This short guide explains how to issue and renew TLS certificates using **Let's Encrypt Certbot**.

---

## 🔹 1. Install Certbot

### Ubuntu/Debian:
```bash
sudo apt update && sudo apt install certbot python3-certbot-nginx
```

### RHEL/CentOS:
```bash
sudo dnf install certbot python3-certbot-nginx
```

### Snap (alternative):
```bash
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

---

## 🔹 2. Issue a New Certificate

**For Nginx:**
```bash
sudo certbot --nginx -d example.com -d www.example.com
```

**For Webroot:**
```bash
sudo certbot certonly --webroot -w /var/www/html -d example.com
```

This:
- Gets certs to `/etc/letsencrypt/live/example.com/`
- Auto-configures Nginx (if using `--nginx`)

---

## 🔹 3. Auto-Renewal

Renew all certs:
```bash
sudo certbot renew
```

Dry-run test:
```bash
sudo certbot renew --dry-run
```

---

## 🔹 4. Troubleshooting Renewal

- **Plugin error**: Install plugin (`python3-certbot-nginx`)
- **Not due yet**: Wait until within 30 days of expiry
- **Hook reload Nginx** (for non-nginx plugin use):
```bash
echo '#!/bin/bash
systemctl reload nginx' | sudo tee /etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh
sudo chmod +x /etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh
```

---

## ✅ Tip

Check certs and expiry:
```bash
sudo certbot certificates
```

Done! Your HTTPS cert should now work and auto-renew reliably.
