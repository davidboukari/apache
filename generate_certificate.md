Source https://admin-serv.net/blog/670/creer-et-installer-un-certificat-ssl-sous-nginx/

Préparation des certificats SSL

Rendez-vous sur votre serveur:

cd /etc/nginx/
# Création d'un dossier ssl pour y mettre les certificats
```bash
mkdir ssl
cd ssl/
```

## Génération des certificats:

# Génération du fichier .key

```bash
openssl genrsa -des3 -out admin-serv.net.key 2048

Generating RSA private key, 2048 bit long modulus
...+++
..................................................................................................+++
e is 65537 (0x10001)
Enter pass phrase for admin-serv.net.key:
Verifying - Enter pass phrase for admin-serv.net.key:
```

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

Déprotection de la clé
```bash
openssl rsa -in admin-serv.net.key -out admin-serv.net.deprotected.key
Enter pass phrase for admin-serv.net.key:
writing RSA key
```

