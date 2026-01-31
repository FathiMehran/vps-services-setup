# Xray + Reality Setup

## Goal
## Prerequisites
## Install Xray
## Generate Keys (Reality)
## Xray Configuration
## Start & Enable Service
## Verification
## Client Configuration Notes
## Security Notes


## Goal
Deploy Xray with Reality protocol for censorship-resistant and stealth communication.

## Prerequisites
- Ubuntu VPS
- Root or sudo access
- A domain (optional but recommended)
- Port 443 open


## Install Xray

Install Xray using the official script:
```bash
bash <(curl -Ls https://github.com/XTLS/Xray-install/raw/main/install-release.sh)


Check installation:

xray version



📌 این اسکریپت:
- binary رو نصب می‌کنه
- systemd service می‌سازه
- مسیر config رو مشخص می‌کنه

---

## 5️⃣ ساخت کلیدهای Reality (خیلی مهم)
```md
## Generate Reality Keys

Generate a key pair:
```bash
xray x25519


Private key: <PRIVATE_KEY>
Public key:  <PUBLIC_KEY>

Save both keys securely.


---

## 6️⃣ کانفیگ Xray (هسته اصلی)
```md
## Xray Configuration

Edit config file:
```bash
sudo nano /usr/local/etc/xray/config.json

Example Reality inbound:

#---------------
{
  "inbounds": [
    {
      "port": 443,
      "protocol": "vless",
      "settings": {
        "clients": [
          {
            "id": "UUID-HERE",
            "flow": "xtls-rprx-vision"
          }
        ],
        "decryption": "none"
      },
      "streamSettings": {
        "network": "tcp",
        "security": "reality",
        "realitySettings": {
          "show": false,
          "dest": "www.cloudflare.com:443",
          "xver": 0,
          "serverNames": [
            "www.cloudflare.com"
          ],
          "privateKey": "PRIVATE_KEY_HERE",
          "shortIds": [
            "abcd1234"
          ]
        }
      }
    }
  ]
}
 
#---------------



📌 جایگزین کن:
- `UUID-HERE`
- `PRIVATE_KEY_HERE`
- `shortIds`

---

## 7️⃣ اجرای سرویس
```md
## Start & Enable Service

Start Xray:
```bash
sudo systemctl start xray

sudo systemctl enable xray

sudo systemctl status xray


---

## 8️⃣ تست و بررسی
```md
## Verification

Check listening port:
```bash
ss -tulnp | grep 443


View logs:

journalctl -u xray -f


---

## 9️⃣ نکات کلاینت (خیلی مهم)
```md
## Client Configuration Notes
- Protocol: VLESS
- Security: Reality
- Flow: xtls-rprx-vision
- Public key: from server
- SNI: www.cloudflare.com ( You can change for yourself )
- Port: 443

## Security Notes
- Do not reuse Reality keys
- Use firewall to allow only required ports
- Keep UUIDs unique per client




