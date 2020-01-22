* .pem 

* .crl ( certificate revocation list) est la liste des identifiants des certificats qui ont été révoqués ou invalidés1 et qui ne sont donc plus dignes de confiance. 

* .key (Private key)

* .csr (Certificate Signing Request) => a request text to generate de certificate 

* .crt (Certificate) 

Source https://admin-serv.net/blog/670/creer-et-installer-un-certificat-ssl-sous-nginx/

## Préparation des certificats SSL

### Création d'un dossier ssl pour y mettre les certificats
```bash
cd /etc/nginx/
mkdir ssl
cd ssl/
```

## Génération des certificats:

### Génération du fichier .key

```bash
openssl genrsa -des3 -out admin-serv.net.key 2048

Generating RSA private key, 2048 bit long modulus
...+++
..................................................................................................+++
e is 65537 (0x10001)
Enter pass phrase for admin-serv.net.key:
Verifying - Enter pass phrase for admin-serv.net.key:
```

### Generation du .csr

Votre fichier .key (protégé par mot de passe) est maintenant créé, nous passons à la génération du CSR:
```bash
openssl req -new -key admin-serv.net.key -out admin-serv.net.csr
# Votre mot de passe saisi plus haut:
Enter pass phrase for admin-serv.net.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:FR
State or Province Name (full name) [Some-State]:France
Locality Name (eg, city) []:Paris
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Admin Serveur
Organizational Unit Name (eg, section) []:Admin Serveur
# C'est la seule question réellement importante
# Elle doit correspondre exactement à votre domaine à sécuriser
Common Name (eg, YOUR name) []:admin-serv.net
Email Address []:postmaster@admin-serv.net

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```
<p>
Votre fichier CSR est désormais créé. Nous allons toutefois en profiter pour déprotéger le fichier KEY afin que NGinx puisse se relancer par la suite tout seul. Vous pouvez en profiter pour vérifer les informations saisies:
</p>

```bash
openssl req -noout -text -in admin-serv.net.csr
[...]
Subject: C=FR, ST=France, L=Paris, O=Admin Serveur, OU=Admin Serveur, CN=admin-serv.net/emailAddress=postmaster@admin-serv.net
[...]
```

### Déprotection de la clé
```bash
openssl rsa -in admin-serv.net.key -out admin-serv.net.deprotected.key
Enter pass phrase for admin-serv.net.key:
writing RSA key
```

### Demande de certificat verifié aupres de NameCheap

<p>
Votre clé n'a maintenant plus de mot de passe bloquant qui aurait gêné un restart de NGinx.
Soumission à NameCheap

Rendez-vous sur NameCheap, dans la section Certificat SSL. Je vous recommande le "RapidSSL Domain Validated" qui nécessite uniquement une validation par email. C'est rapide et simple.

NameCheap va vous demander votre CSR et le type de votre serveur Web. NGinx n'étant pas indiqué vous pouvez mettre n'importe quoi, personnellement je mets Apache 2. Sélectionnez ensuite une adresse email que vous pouvez vérifier, un lien de confirmation vous conduisant chez GeoTrust vous sera transmis.

Cliquez sur ce lien, vous allez, dans les secondes qui suivent recevoir votre certificat SSL (Fichier CRT) par email.

Attention, NameCheap va vous transmettre deux certificats SSL : un nommé "Web Server CERTIFICATE", et un autre nommé "INTERMEDIATE CA". Il va vous falloir les deux pour que le tout fonctionne parfaitement.

Enregistrez le "Web Server CERTIFICATE" dans un fichier nommé "admin-serv.crt", et enregistrez le "INTERMEDIATE CA" dans un fichier nommé "rapidssl.ca.crt".

Nous allons ensuite créé un unique fichier CRT contenant ces deux précédents certificats

cat admin-serv.crt rapidssl.ca.crt > certificate.admin-serv.net.crt

Voilà le tout est prêt, il ne vous reste plus qu'à configurer NGinx correctement pour lui faire utiliser votre certificat
</p>

### Use the certificate with nginx

```bash
cat /etc/nginx/sites-enabled/default

server {
        listen 443;
        server_name admin-serv.net;
        ssl on;
        ssl_certificate /etc/nginx/ssl/certificate.admin-serv.net.crt;
        ssl_certificate_key     /etc/nginx/ssl/admin-serv.net.deprotected.key;
        location / {
                root    /home/www/;
                index   index.php index.html index.htm;
        }
}
```


Relancez ensuite NGinx

```bash
systemctl restart restart
```

### Tests

curl -v -I https://admin-serv.net/
