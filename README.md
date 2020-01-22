# apache

Installation
```bash
yum install httpd
yum install mod_ssl
```

Tree
* [Default tree](tree.md)

Default http configuration
* [default httpd.conf](httpd.conf.md)


Default SSL conf
* [default ssl.conf](ssl.conf.md)

Set certificate and private key default SSL VirtualHost 
```bash
<VirtualHost _default_:443>
  SSLCertificateFile /etc/pki/tls/certs/localhost.crt
  SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

Generate a certificate
* [Generate SSL Certificate](https://github.com/davidboukari/ssl)


Access for all and listing OK
```bash
<Directory />
    #AllowOverride none
    #Require all denied
    Options +Indexes
    Require all granted
</Directory>
```

Reverse Proxy [P] for local request then stay on the local server or remove [P] for redirection to the other server
```
ProxyRequests Off
RewriteEngine on
RewriteCond    %{REQUEST_URI}  .*ProjetTestEnLigne(.*)
RewriteRule    ^.*ProjetTestEnLigne(.*)      http://myIP/ProjetTestEnLigne$1 [P]

RewriteCond    %{REQUEST_URI}  .*PROJETTESTENLIGNE_WEB(.*)
RewriteRule    ^.*PROJETTESTENLIGNE_WEB(.*)      http://myIP/PROJETTESTENLIGNE_WEB$1 [P]
