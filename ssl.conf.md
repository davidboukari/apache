
# SSL Conf
```
tee httpd.conf<<EOF
# ******************************************************************
# * IHS apache SSL config for client.dijon.fr for docker image
# ******************************************************************
LoadModule ssl_module modules/mod_ssl.so
LoadModule rewrite_module modules/mod_rewrite.so

<IfModule mod_ssl.c>
  #/usr/local/apache2
  #bin  build  cgi-bin  conf  error  htdocs  icons  include  logs modules
  Listen 443

  <VirtualHost *:443>
    ServerAdmin admin@myexample.com
    DocumentRoot /usr/local/apache2/htdocs
    ServerName ihs.dijon.fr
    ServerAlias ihs-dijon.fr

    Alias /ihs "/usr/local/apache2/htdocs"

    <Directory /usr/local/apache2/htdocs>
      Options +FollowSymlinks
       AllowOverride All
       Require all granted
       <IfModule mod_dav.c>
         Dav off
       </IfModule>
       SetEnv HOME /usr/local/apache2/htdocs
       SetEnv HTTP_HOME /usr/local/apache2/htdocs

       RewriteEngine   on
       SSLVerifyClient optional_no_ca
       LogLevel warn rewrite:trace8
       RewriteCond    "%{SSL:SSL_CLIENT_S_DN_CN}" "!^(.*client.dijon.fr)$"
       RewriteRule    "^.*" "-" [R=403]

       #RewriteRule    "^.*"  "https://%{HTTP_HOST}/index-notok.html" [R=301,L]
    </Directory>

    SSLCertificateFile /etc/ssl/certs/ihs.dijon.fr.crt
    SSLCertificateKeyFile /etc/ssl/private/ihs.dijon.fr.key
  </VirtualHost>
</IfModule>
EOF
```

# Default ssl configuration
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
