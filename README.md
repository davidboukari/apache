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


### Default SSL conf
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

