1. Title
# V2Ray Behind Cloudflare with Nginx (WebSocket + TLS)
2. Goal
## Goal
Deploy V2Ray behind Cloudflare using Nginx as a reverse proxy with WebSocket and TLS.
3. Prerequisites
## Prerequisites
- Ubuntu VPS
- Root or sudo access
- Domain name added to Cloudflare
- Cloudflare proxy enabled (orange cloud)
- Ports 80 and 443 open
4. DNS and Cloudflare Settings
## Cloudflare Configuration

DNS record:
- Type: A
- Name: yourdomain.com
- IP: VPS public IP
- Proxy status: Proxied (orange cloud)

SSL/TLS settings:
- SSL/TLS mode: Full (strict)
- Always Use HTTPS: Enabled
- Minimum TLS Version: 1.2
5. Install Nginx and Certbot
## Install Nginx and Certbot

Install Nginx:
```bash
sudo apt update
sudo apt install -y nginx
Install Certbot:
sudo apt install -y certbot python3-certbot-nginx

---

## 6. Obtain TLS Certificate
```md
## Obtain TLS Certificate

Issue certificate using Certbot:
```bash
sudo certbot --nginx -d yourdomain.com
Verify auto-renewal:
sudo certbot renew --dry-run

---

## 7. Install V2Ray
```md
## Install V2Ray

Install V2Ray using the official script:
```bash
bash <(curl -Ls https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
Verify installation:
v2ray version

---

## 8. Generate UUID
```md
## Generate UUID

Generate a UUID for the client:
```bash
uuidgen
Save this UUID for client configuration.

---

## 9. V2Ray Configuration (WebSocket)
```md
## V2Ray Server Configuration

Edit V2Ray config:
```bash
sudo nano /usr/local/etc/v2ray/config.json
Example configuration:
{
  "inbounds": [
    {
      "port": 10000,
      "listen": "127.0.0.1",
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "UUID_HERE",
            "alterId": 0
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
          "path": "/ws"
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom"
    }
  ]
}
Replace:
UUID_HERE
WebSocket path if needed

---

## 10. Nginx Configuration
```md
## Nginx Reverse Proxy Configuration

Create Nginx config:
```bash
sudo nano /etc/nginx/sites-available/v2ray.conf
Example configuration:
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    location /ws {
        proxy_redirect off;
        proxy_pass http://127.0.0.1:10000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }
}
Enable site:
sudo ln -s /etc/nginx/sites-available/v2ray.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

---

## 11. Start and Enable V2Ray
```md
## Start and Enable V2Ray

Start V2Ray:
```bash
sudo systemctl start v2ray
Enable on boot:
sudo systemctl enable v2ray
Check status:
sudo systemctl status v2ray

---

## 12. Verification
```md
## Verification

Check listening ports:
```bash
ss -tulnp | grep 10000
Check logs:
journalctl -u v2ray -f
Test WebSocket through Cloudflare.

---

## 13. Client Configuration Notes
```md
## Client Configuration Notes
- Protocol: VMess
- Transport: WebSocket
- Port: 443
- TLS: Enabled
- Host: yourdomain.com
- Path: /ws
- UUID: server-generated UUID
14. Security Notes
## Security Notes
- Do not expose V2Ray directly to the internet
- Always bind V2Ray to localhost
- Use Cloudflare WAF rules if necessary
- Rotate UUIDs periodically

