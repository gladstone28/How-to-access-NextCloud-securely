
Below is the clearest, safest, step-by-step guide to access your Raspberry Pi Nextcloud securely from outside your home.

There are three safe methods, but only one is recommended for beginners.

✅ Use Cloudflare Tunnel (MOST SECURE + easiest)
✅ No port-forwarding
✅ No firewall changes
✅ Free
✅ Automatically HTTPS
✅ Hides your home IP from the public
✅ Works on mobile and desktop
✅ Avoids attacks that normally hit home servers

This is the recommended method.

✅ **METHOD 1 (Recommended): Cloudflare Tunnel — Secure Public Access

(NO port forwarding, NO exposing your home network)**

What you need

A free Cloudflare account

A domain name (you can buy one cheaply anywhere: Namecheap, Google Domains, Cloudflare, etc.)

Your Raspberry Pi running Nextcloud

Step 1 — Create a Free Cloudflare Account

Go to:

https://dash.cloudflare.com

Sign up.

Step 2 — Add Your Domain to Cloudflare

If your domain is:

myfamilycloud.com


Cloudflare will give you nameservers to enter at your domain registrar.

Change nameservers → wait 5–10 minutes.

Step 3 — Install Cloudflare Tunnel on Raspberry Pi

Run:

curl -fsSL https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64.deb -o cloudflared.deb
sudo dpkg -i cloudflared.deb


Install as a service:

sudo cloudflared service install

Step 4 — Authenticate your Pi with Cloudflare

Run:

cloudflared tunnel login


This opens a URL.
Open the URL on a laptop or phone → select your domain.

Step 5 — Create the Tunnel

Run:

cloudflared tunnel create nextcloud-pi


Cloudflare will generate a tunnel ID.

Step 6 — Route a Public Hostname to Your Pi

For example, you want:

https://cloud.mydomain.com


Run:

cloudflared tunnel route dns nextcloud-pi cloud.mydomain.com

Step 7 — Create Tunnel Config File

Edit:

sudo nano /etc/cloudflared/config.yml


Paste:

tunnel: nextcloud-pi
credentials-file: /root/.cloudflared/<YOUR-TUNNEL-ID>.json

ingress:
  - hostname: cloud.mydomain.com
    service: http://127.0.0.1:80
  - service: http_status:404


Save → exit.

Step 8 — Start the Tunnel
sudo systemctl start cloudflared
sudo systemctl enable cloudflared

✅ Now test:

Open:

https://cloud.mydomain.com


Nextcloud should load securely with:

✅ Automatic HTTPS
✅ No port forwarding
✅ No exposure of your home internet IP
✅ Cloudflare firewall + DDoS protection
✅ Works anywhere in the world

✅ OPTIONAL — Secure Nextcloud Trusted Domains

Edit:

sudo nano /var/www/nextcloud/config/config.php


Add:

  2 => 'cloud.mydomain.com',


Save → exit → restart web server.

✅ Done.

Your Nextcloud is now fully secure and accessible from anywhere.


