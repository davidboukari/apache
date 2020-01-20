# apache

Installation
```bash
yum install httpd
yum install mod_ssl
```

Tree
```bash
cd /etc/httpd
.
./conf
./conf/httpd.conf
./conf/magic
./conf.d
./conf.d/README
./conf.d/autoindex.conf
./conf.d/userdir.conf
./conf.d/welcome.conf
./conf.d/ssl.conf
./conf.modules.d
./conf.modules.d/00-base.conf
./conf.modules.d/00-dav.conf
./conf.modules.d/00-lua.conf
./conf.modules.d/00-mpm.conf
./conf.modules.d/00-proxy.conf
./conf.modules.d/00-systemd.conf
./conf.modules.d/01-cgi.conf
./conf.modules.d/00-ssl.conf
./logs
./modules
./run
```

Default SSL conf
```bash
grep "^[^#]" conf.d/ssl.conf
Listen 443 https
SSLPassPhraseDialog exec:/usr/libexec/httpd-ssl-pass-dialog
SSLSessionCache         shmcb:/run/httpd/sslcache(512000)
SSLSessionCacheTimeout  300
SSLRandomSeed startup file:/dev/urandom  256
SSLRandomSeed connect builtin
SSLCryptoDevice builtin
<VirtualHost _default_:443>
ErrorLog logs/ssl_error_log
TransferLog logs/ssl_access_log
LogLevel warn
SSLEngine on
SSLProtocol all -SSLv2 -SSLv3
SSLCipherSuite HIGH:3DES:!aNULL:!MD5:!SEED:!IDEA
SSLCertificateFile /etc/pki/tls/certs/localhost.crt
SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
<Files ~ "\.(cgi|shtml|phtml|php3?)$">
    SSLOptions +StdEnvVars
</Files>
<Directory "/var/www/cgi-bin">
    SSLOptions +StdEnvVars
</Directory>
BrowserMatch "MSIE [2-5]" \
         nokeepalive ssl-unclean-shutdown \
         downgrade-1.0 force-response-1.0
CustomLog logs/ssl_request_log \
          "%t %h %{SSL_PROTOCOL}x %{SSL_CIPHER}x \"%r\" %b"
</VirtualHost>
```

For default SSL VirtualHost 
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

