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
