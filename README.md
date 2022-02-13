# apache

### Installation
```bash
yum install httpd
yum install mod_ssl
```

### Tree
* [Default tree](tree.md)

### Default http configuration
* [default httpd.conf](httpd.conf.md)

## Eliminate unwanted headers

```bash
ServerTokens Prod
ServerSignature Off
```

## Test config
* 
```
Test your Apache configuration file before restarting.
As a best practice, check your Apache configuration file for any errors before restarting Apache.
    Caution: Apache won't start again if your configuration files have syntax errors.
Run the following command to test your configuration file (on some systems, it's apache2ctl):

apachectl configtest

Restart Apache. 
```

### Default SSL conf
* [default ssl.conf](ssl.conf.md)

Set certificate and private key default SSL VirtualHost 
* https://www.digicert.com/kb/csr-ssl-installation/apache-openssl.htm
```bash
<VirtualHost 192.168.0.1:443>
    DocumentRoot /var/www/html2
    ServerName www.yourdomain.com
        SSLEngine on
        SSLCertificateFile /path/to/your_domain_name.crt
        SSLCertificateKeyFile /path/to/your_private.key
        SSLCertificateChainFile /path/to/DigiCertCA.crt
</VirtualHost>

or 
<VirtualHost _default_:443>
  SSLCertificateFile /etc/pki/tls/certs/localhost.crt
  SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

Generate a certificate
* [Generate SSL Certificate](https://github.com/davidboukari/ssl)
* [Other link](generate_certificate.md)


### Access for all and listing OK
```bash
<Directory />
    #AllowOverride none
    #Require all denied
    Options +Indexes
    Require all granted
</Directory>
```

### Reverse proxy

Reverse Proxy [P] for local request then stay on the local server or remove [P] for redirection to the other server

```bash
ProxyRequests Off
RewriteEngine on
RewriteCond    %{REQUEST_URI}  .*ProjetTestEnLigne(.*)
RewriteRule    ^.*ProjetTestEnLigne(.*)      http://myIP/ProjetTestEnLigne$1 [P]

RewriteCond    %{REQUEST_URI}  .*PROJETTESTENLIGNE_WEB(.*)
RewriteRule    ^.*PROJETTESTENLIGNE_WEB(.*)      http://myIP/PROJETTESTENLIGNE_WEB$1 [P]
```

### Load Balancer

```balancer
a2enmode proxy_balancer  lbmethod_byrequests  proxy_ajp
ProxyRequests Off
<Proxy \*>
  Order deny,allow
  Deny from all
</Proxy>
<Proxy "balancer://mycluster">
    BalancerMember "http://www.formationweb.local"     route=1
    BalancerMember "http://www.test.local"             route=2
</Proxy>
ProxyPass "/test" "balancer://mycluster"
ProxyPassReverse "/test" "balancer://mycluster"
```

## debian apache php
```
apt-get update
apt-get install php libapache2-mod-php

```
