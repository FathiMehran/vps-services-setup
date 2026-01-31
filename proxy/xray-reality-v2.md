1. Title
# Xray + Reality Setup
2. Goal
## Goal
Deploy Xray using the Reality protocol on port 4433 to provide a stealthy and censorship-resistant connection.
3. Prerequisites
## Prerequisites
- Ubuntu VPS
- Root or sudo access
- A registered domain name
- Port 4433 open on the server firewall
4. Install Xray
## Install Xray

Install Xray using the official installation script:
```bash
bash <(curl -Ls https://github.com/XTLS/Xray-install/raw/main/install-release.sh)
Verify installation:
xray version

---

## 5. Generate UUID (Client ID)
```md
## Generate UUID

Generate a UUID for the client:
```bash
xray uuid
Example output:
c8c7b6a2-1f3e-4c55-bd8a-xxxxxxxxxxxx
Each client should have a unique UUID.

---

## 6. Generate Reality Key Pair
```md
## Generate Reality Keys

Generate Reality key pair:
```bash
xray x25519
Example output:
Private key: <PRIVATE_KEY>
Public key:  <PUBLIC_KEY>
Store the private key securely. The public key will be used by clients.

---

## 7. Xray Configuration
```md
## Xray Configuration

Edit the Xray configuration file:
```bash
sudo nano /usr/local/etc/xray/config.json
Example inbound configuration (Reality on port 4433):
{
  "inbounds": [
    {
      "port": 4433,
      "protocol": "vless",
      "settings": {
        "clients": [
          {
            "id": "UUID_HERE",
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
          "dest": "yourdomain.com:443",
          "xver": 0,
          "serverNames": [
            "yourdomain.com"
          ],
          "privateKey": "PRIVATE_KEY_HERE",
          "shortIds": [
            "a1b2c3d4"
          ]
        }
      }
    }
  ]
}
Replace the following values:
UUID_HERE
PRIVATE_KEY_HERE
yourdomain.com
shortIds

---

## 8. Start and Enable Xray
```md
## Start and Enable Service

Start Xray:
```bash
sudo systemctl start xray
Enable Xray on boot:
sudo systemctl enable xray
Check service status:
sudo systemctl status xray

---

## 9. Verification
```md
## Verification

Check listening port:
```bash
ss -tulnp | grep 4433
View logs:
journalctl -u xray -f

---

## 10. Client Configuration Notes
```md
## Client Configuration Notes
- Protocol: VLESS
- Transport: TCP
- Security: Reality
- Port: 4433
- SNI: yourdomain.com
- Public Key: server public key
- Flow: xtls-rprx-vision
11. Security Notes
## Security Notes
- Use a unique UUID per client
- Do not reuse Reality key pairs
- Restrict open ports using a firewall
- Avoid exposing the Xray management interface

