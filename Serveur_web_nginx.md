# Serveur web Nginx
- [Serveur web Nginx](#serveur-web-nginx)
  - [Préface](#préface)
  - [Mise en place du système](#mise-en-place-du-système)
  - [Installation de NGINX et de PHP-FPM](#installation-de-nginx-et-de-php-fpm)
    - [Dnf et les modules](#dnf-et-les-modules)
  - [Configuration primaire du site web](#configuration-primaire-du-site-web)
  - [Récupération des clefs d'API OVH](#récupération-des-clefs-dapi-ovh)
## Préface
[Nginx](https://fr.wikipedia.org/wiki/NGINX) est un serveur web très populaire dans le domaine du cloud pour sa meilleure gestion des fortes charges et pour sa configuration très modulable (il est par ailleurs très bien optimisé pour du [reverse-proxy](https://fr.wikipedia.org/wiki/Proxy_inverse)).

PHP-FPM est le module permettant au serveur web Nginx d'exécuter des pages codées en PHP.

[Acme.sh](https://acme.sh) est un script permettant d'obtenir des certificats pour des serveurs web en exploitant le protocole acme.

Le [CAA](https://fr.wikipedia.org/wiki/DNS_Certification_Authority_Authorization) est un type d'enregistrement DNS permettant à ces derniers de lister les Autorités de Certification (CA) pouvant délivrer un certificat au nom de domaine correspondant.

Dans notre cas, notre système tourne avec un CentOS 8 migré sous AlmaLinux, donc, une base Red Hat.

## Mise en place du système

Dans le cas où l'on viens d'installer / d'obtenir un serveur (dédié physique ou serveur privé virtuel), une mise à jour s'impose. Sous les bases RedHat, on y trouve le gestionnaire de paquet dnf. Concrètement, dnf est aux bases RedHat ce qu'apt est aux bases Debian. Ceci expliqué, je ne vais cependant pas détailler la migration d'une CentOS 8 à AlmaLinux, je vous invite donc à consulter leur [guide de migration](https://github.com/AlmaLinux/almalinux-deploy). 

Pour mettre à jour les paquets sous une base RHEL, exécutez la commande `dnf upgrade -y` dans la console. Dans le cas où vous êtes connecté à un utilisateur non root, mais ayant les permissions sudo, exécutez la même commande précédée de `sudo`.

## Installation de NGINX et de PHP-FPM

Avant d'installer les paquets, une courte notion sur les modules de dnf.

### Dnf et les modules

Dnf permet d'installer différentes versions d'un même paquet. Par défaut, c'est la version encore maintenue la plus mature et déployée qui est installée. Afin de s'assurer qu'il s'agisse de la dernière version de Nginx qui est installée, exécuter les commandes suivantes dans le terminal :

```bash
dnf module nginx switch-to 1.18

dnf install nginx:1.18 -y
```

Ensuite, l'installation de PHP-FPM est très aisée, car il n'y a pas de modules pour cette dernière. Exécutez la commande `dnf install php-fpm -y`

## Configuration primaire du site web

Avant d'avoir un serveur web parfaitement fonctionnel et sécurisé avec l'HTTPS, on va d'abord poser la base du serveur web sur lequel nous allons héberger notre site. Dans notre exemple, il s'agit du site du serveur GTA RP Les Rescapés RP.

Dans les systèmes Linux sous base Debian, les configurations des sites et des hôtes virtuels d'Nginx sont localisés dans `/etc/nginx/sites-available`, et afin de rendre les sites dont leurs configurations sont dans ce répertoire, par convention, un lien symbolique vers le fichier de configuration du site est fait dans `/etc/nginx/sites-enabled`. Un lien symbolique s'utilise de cette façon : `ln -s /etc/nginx/sites-available/kiwindows.fr /etc/nginx/sites-enabled/kiwindows.fr`

Cependant, dans les bases Rhel (RedHat), ces deux répertoires n'existent pas par défaut, il faut donc d'abord créer un répertoire similaire puis configurer Nginx de manière à ce qu'il prenne en compte les fichiers de configuration contenus dans ce nouveau répertoire.

Comme nous allons travailler à présent essentiellement dans le répertoire d'Nginx, on va changer notre terminal de répertoire courant : `cd /etc/nginx`. Ensuite, on va créer ce nouveau répertoire que l'on va nommer "sites" avec la commande `mkdir sites`. Note : si la commande a échouée car vous n'avez pas les permissions, vous devez alors l'exécuter avec les permissions de super-utilisateur. Petit raccourci sous Linux, afin de vous épargner de retaper manuellement la commande, entrez `sudo !!` dans le terminal pour lancer la précédente commande exécutée avec sudo. Sous les systèmes Linux, `!!` représente la dernière commande exécutée, donc dans notre exemple, elle viens à faire `sudo mkdir sites` dans le cas où `mkdir sites` a échoué auparavant.

Ensuite, nous allons modifier le fichier de configuration générale d'Nginx, afin qu'il puisse prendre en compte les fichiers de configuration qui seront dans ce nouveau dossier. Pour cela, nous allons éditer son fichier de configuration avec l'éditeur Nano, qui est un éditeur en ligne de commande sous Linux facile à utiliser, notamment pour les novices. Lancez alors la commande `nano nginx.conf` dans le cas où vous êtes connecté à l'utilisateur root, ou `sudo nano nginx.conf` dans le cas où vous êtes connecté à un utilisateur non-root mais ayant les permissions sudo.

Enfin, dans le fichier de configuration, descendre plus bas jusqu'à trouver le blox "http", puis ajouter la ligne `include /etc/nginx/sites/*;`. N'oubliez pas la tabulation,comme suit sur l'image ci-dessous :

![](images/conf_nginx.png)

Sauvegarder le fichier en pressant les touches ctrl et x, pressez la touche y pour confirmer, puis entrée pour valider. Le fichier de configuration d'Nginx édité, il va falloir que ce dernier prenne en compte la modification. Mais avant toute chose, il est toujours préférable de demander à Nginx de vérifier si le fichier de configuration fraîchement modifé est correct et ne comporte pas d'erreurs. Pour cela, rentrer dans le terminal la commande `nginx -t`, puis Nginx vous dira si le fichier est valide où s'il comporte une ou des erreurs, et vous dira même où !. Dans notre cas, la modification n'étant pas très poussée, tout devrait bien se passer.

Ainsi, nous pouvons demander à Nginx de recharger son fichier de configuration avec `nginx -s reload`.

Désormais, nous pouvons créer le fichier de configuration de notre site web. Globalement, et à ce stade, une configuration très élémentaire nous suffira amplement. Cette fernière doit nous permettre :
- De définir la localisation des fichiers de notre site (ici `/var/www/lesrescapesrp.fr`, qui est un répertoire que l'on va créer)
- Lui donner des noms d'hôte, qui servira plus tard notamment en ce qui est matière de certificats.
- Le tout de fonctionner sur le port 80, qui est le port par défaut pour de l'HTTP.

De ce fait, nous allons créer le fichier de configuration de notre site web avec `nano sites/lesrescapesrp.fr` (n'oubliez pas le sudo avant si vous n'êtes pas sur l'utilisateur root, mais n'oubliez pas également de nommer votre fichier de site en fonction de vos besoins. Il est courant de nommer le fichier de configuration de votre site avec le nom d'hôte qui va lui être attribué). Enfin, copiez la configuration suivante dans votre éditeur de texte :
```nginx configuration
server {
    listen 80; # Ici on demande à Nginx d'écouter le port sur toutes les adresses IPv4 de toutes les interfaces réseau.
    listen [::]:80; # Idem, mais avec les IPv6 de toutes les interfaces réseau.

    server_name lesrescapesrp.fr www.lesrescapesrp.fr; # On donne deux noms d'hôtes à notre site, car certains navigateurs "forcent" l'utilisation du préfixe www.

    root /var/www/lesrescapesrp.fr; # La localisation de nos fichiers de site web. Notes que "lesrescapesrp.fr" est un répertoire et non un fichier.

    location / {
      index index.html index.php;
    } # Ce bloc ordonne à Nginx, lorsqu'une requête s'adresse à ce serveur web, de retourner en priorité, dans la conditions où ils existent, les fichiers index.html et index.php.
}
```
Évidemment, changez les paramètres dans ce fichier selon vos besoins. Ensuite, il est de coutume de toujours tester nos fichiers de configuration Nginx après avoir opéré à un quelconque changement, avec `nginx -t`. Comme précédemment, si cette commande vous renvoie des erreurs, vérifiez votre configuration. Dans le cas contraire, nous pouvons demander à Nginx de recharger les fichiers de configuration en mémoire pour qu'il prenne en compte les changements avec `nginx -s reload`.

Si le répertoire dans `/var/www` cité plus haut n'est toujours pas créé, faites-le toujours avec la commande mkdir, dans notre cas `mkdir /var/www/lesrescapesrp.fr`. Rappel : dans cette commande, nous mettons le chemin dit absolu du répertoire car pour rappel, nous travaillons toujours présentement dans le répertoire `/etc/nginx` et y resterons dans le sens où nous nous allons être ammenés à éditer à nouveau les configurations d'Nginx plus tard. 2e rappel : adaptez la création de votre répertoire de site en fonction de ce que vous avez précédemment convenu dans le fichier de configuration de votre site.

Enfin, vous pouvez, au choix :
- Directement ajouter vos fichiers de site dans le répertoire nouvellement créé, auquel cas passez par le protocole SFTP qui est non seulement plus sûr mais également plus simple d'utilisation
- Ou alors créer un bref fichier html dans ce répertoire : `nano /var/www/lesrescapesrp.fr/index.html` dans lequel ce dernier contient la seule et unique ligne `<h1>test</h1>`.

Enfin, vous pouvez tester votre site en entrant dans la barre d'URL de votre navigateur l'adresse IP de votre serveur. Normalement, votre navigateur devrait, en fonction de ce que vous avez fait, soit vous renvoyer votre site, soit vous renvoyer un "test" en gros et gras, en haut à gauche de votre fenêtre.

## Récupération des clefs d'API OVH

Un serveur web qui fonctionne et qui renvoie notre site, c'est bien. Mais devoir donner une adresse IP pour y accéder, c'est moins bien. Mais summum du firmament du comble de l'apogée de l'excès de la connerie à ne pas faire, surtout en 2021 là où ce qui suit est gratuit et facile à mettre en place, en plus de devenir "obligatoire" : laisser son site tourner en HTTP clair, et non en HTTPS, c'est pas seulement pas bien, c'est très moche, vous avez une place attibuée en enfer si vous laissez une chose pareille en production.

Pour pallier ce problème, nous allons déployer des certificats sur notre serveur web. Notez que cette étape de "clefs d'API OVH" n'est nécessaire que dans le cas où vous souhaitez avoir des certificats supportant les wildcard (ex. *.lesrescapesrp.fr, pour avoir pleins de sous-domaines pouvant exploiter ces mêmes certificats). Prenez en compte que ce ne sont pas toutes les Autorités de Certifications qui peuvent délivrer gratuitement des certificats wildcard.