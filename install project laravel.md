# Install Project Laravel
Ok super
## Télécharger

> Putty https://the.earth.li/~sgtatham/putty/latest/w64/putty-64bit-0.73-installer.msi

> Virutal Box https://download.virtualbox.org/virtualbox/6.1.4/VirtualBox-6.1.4-136177-Win.exe

> Unbuntu Server
https://ubuntu.com/download/server/thank-you?version=18.04.4&architecture=amd64

## Installation Ubuntu sur Virtual Box

1. Une fois Ubuntu Server téléchargé, lancez Virtual Box et l'installer

2. Cliquez sur `Nouvelle`

3. Choisir le nom que l'on veut lui attribuer Exemple (UbuntuServerHome) Veillez bien à ce que le type soit sur Linux et la version sur Ubuntu. Ensuite cliquez sur `Continuer`.

4. On doit ensuite indiquer la quantité de mémoire vive de notre ordinateur que l'on veut allouez à cette machine virtuelle (2048) puis cliquez sur `suivant`.

5. Pour la suite il suffit de valider la proposition standard de `Créer un disque virtuel` et ensuite de cliquer sur `Créer`.

6. il suffit ensuite de laisser le type de fichier de disque dur en VDI et de cliquer sur `Continuer`

7. Ensuite Stocker le disque dur dynamiquement alloué, pour gagner du temps et pas utiliser trop de place puis cliquez sur `suivant`.

8. Valider la taille de 10Gio et cliquez sur `Crée`

9. Ensuite cliquez sur `Configuration` > `Système` > `Processeur` et augmenter le nombre de processeur de 1 à 2 puis cliquez sur `OK`

10. Puis cliquez sur `Configuration` de nouveau et dans `Réseau` > `Avancé` > `Redirection de ports` et insérer deux nouvelle règles de redirection comme sur l'image ci-dessous
![](https://i.imgur.com/p1H7of0.png)
Puis cliquez sur `OK` puis `OK` à nouveau.

11. Cliquez maintenant sur `Démarrer` et sur l'icone de dossier à droite du menu déroulant et sur `Ajouter`
Séléctionner le fichier Ubuntu télécharger auparavant, il est de type `.iso` Puis faire > `Choisir` > `démarrer`

12. Dans un premier temps l’installation va se lancer et charger jusqu’au choix de la langue. L’installation se fait en ligne de commande, donc pas de souris, utilisez les pavés directionnels pour se déplacer.

13. Cliquez sur `Terminer`

14. Suivez les instructions et validez les choix standard jusqu’à la Configuration du système de fichiers puis choisissez `Utilisez un disque entier` et ensuite valider le nom du disque créé par Virtual Box, le nom commence par `VBOX_HARDDISK_` 
Validez ensuite les choix par défaut jusqu’à la Configuration du profil.

15. Il vous suffit de remplir les champs comme demandés. A prendre en compte que le Username sera aussi votre identification de connexion en SSH par exemple et au moment de taper votre Mot de passe, éviter d’utiliser le pavé numérique…. Cela peut vous jouer des tours…
Si vous voulez vous connecter en SSH à ce serveur local, installer le server OpenSSH, sans Import SSH Identity.
Ensuite il n’est pas nécessaire d’installer d’autres features, puis l’installation se lance et il faut maintenant patienter et attendre que celle-ci se termine.

16. Un fois l’installation terminée, entrez sur `reboot`.
Ensuite à l’affichage de l’écran suivant, appuyez simplement sur Entrée
Ensuite le système reboot et Ubuntu server se lance. Puis il vous suffit d’entrer votre Login et votre mot de passe pour ouvrir votre session.

17. taper `sudo apt install openssh-server` pour pouvoir se connecter avec Putty sur le serveur

## Putty

1. Lancer Putty, taper l'adresse ip de redirection de port auparavant renseigner (127.0.0.1) et faire `open` et `oui`

2. Connecter vous avec le même login et mot de passe que sur la VirtuelMachine.

## Installer Nginx

1. Faire `sudo apt update` puis `sudo apt install nginx`

2. Faire ensuite `sudo ufw allow 'Nginx HTTP'` et un `sudo ufw status` pour verifier le status actuel, si il et incatif il faut taper `sudo ufw enable`. Ensuite il faut taper `sudo ufw allow (ssh ou '22')` Et refaire un `sudo ufw status` pour vérifier que le port HTTP et le port SSH soit activé.

3. taper dans le navigateur '127.0.0.1:8080' et la page Welcome to Nginx! devrait s'afficher puis retourner sur Putty.

## Installer MySQL

1. pour installer mysql il faut faire `sudo apt install mysql-server`

2. Sécuriser l'installation en faisant `sudo mysql_secure_installation` Ce script vous demandera si vous souhaitez configurer le VALIDATE PASSWORD PLUGIN. Répondez `Y` par oui, ou toute autre chose pour continuer sans l'activer. Si vous avez activé la validation, le script vous demandera également de sélectionner un niveau de validation de mot de passe. Puis il vous sera demandé de soumettre et de confirmer un mot de passe root. Pour le reste des questions, vous devez appuyer `Y` et appuyer sur la `ENTER` touche à chaque invite.

4. Si l'utilisation du `auth_socket` plugin pour accéder à MySQL correspond à votre flux de travail, vous pouvez passer cette étape. Si, cependant, vous préférez utiliser un mot de passe lors de la connexion à MySQL en tant que root , vous devrez changer sa méthode d'authentification de auth_socketà mysql_native_password. Pour ce faire, ouvrez l'invite MySQL depuis votre terminal avec `sudo mysql` Par la suite en tapant `SELECT user,authentication_string,plugin,host FROM mysql.user;` Il vous afficher un tableau dans lequel vous pouvez appercevoir quelle méthode d'authentification chacun de vos comptes d'utilisateurs MySQL utilise. Faite `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';` et renseigner le password ( Assurez-vous de changer passwordpour un mot de passe fort de votre choix) Ensuite, exécutez `FLUSH PRIVILEGES` ce qui indique au serveur de recharger les tables de droits et de mettre vos nouvelles modifications en vigueur `FLUSH PRIVILEGES;` Vérifiez à nouveau les méthodes d'authentification utilisées par chacun de vos utilisateurs pour confirmer que root ne s'authentifie plus à l'aide du `auth_socketplugin` en faisant `SELECT user,authentication_string,plugin,host FROM mysql.user;` et enfin quitter le shell MySQL en tapant `exit`
> Remarque : Après avoir configuré votre utilisateur racine MySQL pour s'authentifier avec un mot de passe, vous ne pourrez plus accéder à MySQL avec la sudo mysqlcommande utilisée précédemment. Au lieu de cela, vous devez exécuter ce qui suit:
`mysql -u root -p`
Après avoir entré le mot de passe que vous venez de définir, vous verrez l'invite MySQL.

## Installer PHP et configurer Nginx pour utiliser le processeur PHP

1. taper `sudo apt install php-fpm php-mysql`

2. Il va falloir copié le fichier default de configuration bloc server à présent. Pour cela taper `cd /etc/nginx/sites-available/` et faite `ls` pour voir le fichier default présent. Il faut le copié, pour cela on fait `sudo cp default nomDeLaCopie` refaite ensuite un `ls` pour visualiser la copie réaliser. A partir de maintenant il va falloir modifier la copie en tapant `sudo nano nomDeLaCopie` Une fois à l'interieur déplacer vous avec les flèches directionel allez a la ligne `root /var/www/html` et ajouter à la suite du `html` ceci : `html/public` et aller la ou ce trouve la ligne `index index.html index.html index.nginx-debian.html;` et ajouter `index.php` juste après le premier `index`. Ensuite aller à la ligne `location ~ \.php$ {` et supprimé le symbole `#` qui se situe devant cete ligne (c'est ce qui met le code en commentaire) supprimé le même symbole de la ligne juste en dessous et celle de la ligne `fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;` et modifier cette ligne mettre `php7.2` au lieu de `php7.0` Puis descendre supprimé le même symbole `#` devant la fermeture de l'accolade de la location dans laquelle on ce situe. Supprimé également ce symbole devantla ligne `location ~ /\.ht {` et la fermeture de la même manière que pour la précédente. Voilà le fichier et modifier il faut maintenant le fermer et l'enregistrer en tapan `CTRL+X` et faire `y` et `ENTER`.

3. Activez votre nouveau bloc serveur en créant un lien symbolique de votre nouveau fichier de configuration de bloc serveur (dans le /etc/nginx/sites-available/répertoire) vers le /etc/nginx/sites-enabled/répertoire en tapant `sudo ln -s /etc/nginx/sites-available/nomDeLaCopie /etc/nginx/sites-enabled/` Ensuite, dissociez le fichier de configuration par défaut du /sites-enabled/répertoire en tapant `sudo unlink /etc/nginx/sites-enabled/default` Et testez votre nouveau fichier de configuration pour les erreurs de syntaxe en tapant `sudo nginx -t` Si des erreurs sont signalées, revenez en arrière et revérifiez votre fichier avant de continuer. Lorsque vous êtes prêt, rechargez Nginx pour apporter les modifications nécessaires en faisant `sudo systemctl reload nginx`

(a tester `sudo service nginx restart`)

## Création d'un fichier PHP pour tester la configuration

Crée un fichier info.php en faisant `cd ../../../` Puis `cd /var/www/html/` et enfin `sudo nano info.php` et taper dedans les lignes 
```
<?php
phpinfo();
```

Allez sur votre navigateur et faite `127.0.0.1:8080/info.php` Si la page s'affiche c'est que ça fonctionne bien sinon il est possible que les étapes précédente non pas été réaliser comme il faut.

Si tout cela fonctionne bien on peut supprimer le fichier info.php en tapant `sudo rm /var/www/html/info.php`

Puis installer les dépendances de php7.2 en tapant 
> sudo apt install php7.2-common php7.2-cli php7.2-gd php7.2-mysql php7.2-curl php7.2-intl php7.2-mbstring php7.2-bcmath php7.2-imap php7.2-xml php7.2-zip

## Installer Composer

Effectuer ces commande

> sudo php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"

> sudo php -r "if (hash_file('sha384', 'composer-setup.php') === 'e0012edf3e80b6978849f5eff0d4b4e4c79ff1609dd1e613307e16318854d24ae64f26d17af3ef0bf7cfb710ca74755a') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"

> sudo php composer-setup.php

> sudo php -r "unlink('composer-setup.php');"

Puis faire `sudo mv composer.phar /usr/local/bin/composer`

Et `cd /var/www/html/`

Ensuite cloner le repo en faisant `sudo git clone https://github.com/aurelie-parcero/Pimp-my-shoes.git`
 Allez à la racine en tapan `cd ../../../`

Et faire
> chmod -R 777 /var/www/html/Pimp-my-shoes

Puis retourner dans le projet en tapan `cd /var/www/html/Pimp-my-shoes`

Et installer composer en tapan `composer install`

Générer la clé avec `sudo php artisan key:generate`

Il faut maintenant copié le fichier `.env.exemple` en `.env` en faisan `sudo cp .env.example .env` et le modifier donc taper `sudo nano .env`.

Il faut maintenant renseigner les champs DB_DATABASE, DB_USERNAME, DB_PASSWORD.

Enregistrer le fichier comme tout à l'heur en faisan `CTRL+X` et `y` puis `ENTER`

Maintenant faite `mysql -u root -p
` pour entrer dans mysql

Il faut crée la base de donné faite `CREATE DATABASE pimp_my_shoes`

Crée un nouvelle utilisateur ayant le même DB_USERNAME et DB_PASSWORD que dans le .env
faite `CREATE USER ‘pikachu’@‘localhost’ IDENTIFIED BY 'pikapika';`

Ensuite il faut lui donner les privilèges`GRANT ALL ON pimp_my_shoes.* TO 'pikachu'@'localhost' IDENTIFIED BY 'pikapika' WITH GRANT OPTION;`

Et ensuite il suffit de sortir de mysql avec `exit` et faire un `sudo php artisan migrate`