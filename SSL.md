sudo certbot certonly --manual --preferred-challenges=dns --email shiv.m.arna@gmail.com  --server https://acme-v02.api.letsencrypt.org/directory --agree-tos -d siddhvastu.com -d *.siddhvastu.com

_acme-challenge.siddhvastu.com.

sAzWnEQEmmfdNE5__sl5eJ07tWhfVvP3UASZl_JhRRM

IBToOfy0hOPZn7SIW_I-o0ZCo8iEqEAI5Z745bL95Ew

-----------------------------------------------------------
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/siddhvastu.com/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/siddhvastu.com/privkey.pem
This certificate expires on 2025-04-10.

------------------------------------------------------------------------------
<VirtualHost *:443>
    ServerName www.siddhvastu.com

    SSLEngine on
# for socket
    RewriteEngine On
    RewriteCond %{HTTP:Upgrade} =websocket [NC]
    RewriteRule /(.*)           ws://localhost:3001/$1 [P,L]
    RewriteCond %{HTTP:Upgrade} !=websocket [NC]
    RewriteRule /(.*)           http://localhost:3001/$1 [P,L]

    # ProxyPass and ProxyPassReverse to forward requests
    ProxyPass / http://localhost:3001/
    ProxyPassReverse / http://localhost:3001/

    # Allow CORS
    <Location />
        Header set Access-Control-Allow-Origin "*"
    </Location>

ErrorLog ${APACHE_LOG_DIR}/proxy_error.log
CustomLog ${APACHE_LOG_DIR}/proxy_access.log combined

SSLCertificateFile /etc/letsencrypt/live/siddhvastu.com/fullchain.pem
SSLCertificateKeyFile /etc/letsencrypt/live/siddhvastu.com/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf
</VirtualHost>
