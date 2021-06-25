# Serveur web Nginx
- [Serveur web Nginx](#serveur-web-nginx)
  - [Préface](#préface)
  - [Mise en place du système](#mise-en-place-du-système)
  - [Installation de NGINX et de PHP-FPM](#installation-de-nginx-et-de-php-fpm)
    - [Dnf et les modules](#dnf-et-les-modules)
  - [Configuration primaire du site web](#configuration-primaire-du-site-web)
## Préface
[Nginx](https://fr.wikipedia.org/wiki/NGINX) est un serveur web très populaire dans le domaine du cloud pour sa meilleure
gestion des fortes charges et pour sa configuration très modulable (il est par
ailleurs très bien optimisé pour du [reverse-proxy](https://fr.wikipedia.org/wiki/Proxy_inverse)).  
PHP-FPM est le module permettant au serveur web Nginx d'exécuter des pages codées en PHP.

[Acme.sh](https://acme.sh) est un script permettant d'obtenir des certificats pour des serveurs web en exploitant le protocole acme.

Le [CAA](https://fr.wikipedia.org/wiki/DNS_Certification_Authority_Authorization) est un type d'enregistrement DNS
permettant à ces derniers de lister les Autorités de Certification (CA) pouvant délivrer un certificat au nom de domaine correspondant.

Dans notre cas, notre système tourne avec un CentOS 8 migré sous AlmaLinux, donc, une base Red Hat.

## Mise en place du système

Dans le cas où l'on viens d'installer / d'obtenir un serveur (dédié physique ou serveur privé virtuel), une mise à jour
s'impose. Sous les bases RedHat, on y trouve le gestionnaire de paquet dnf. Concrètement, dnf est aux bases RedHat ce qu'apt
est aux bases Debian. Ceci expliqué, je ne vais cependant pas détailler la migration d'une CentOS 8 à AlmaLinux,
je vous invite donc à consulter leur [guide de migration](https://github.com/AlmaLinux/almalinux-deploy). 

Pour mettre à jour les paquets sous une base RHEL, exécutez la commande `dnf upgrade -y` dans la console.
Dans le cas où vous êtes connecté à un utilisateur non root, mais ayant les permissions sudo, exécutez la même
commande précédée de `sudo`.

## Installation de NGINX et de PHP-FPM

Avant d'installer les paquets, une courte notion sur les modules de dnf.

### Dnf et les modules

Dnf permet d'installer différentes versions d'un même paquet. Par défaut, c'est la version encore maintenue la plus mature
et déployée qui est installée. Afin de s'assurer qu'il s'agisse de la dernière version de Nginx qui est installée, exécuter les
commandes suivantes dans le terminal :

`dnf module nginx switch-to 1.18`

`dnf install nginx:1.18 -y`

Ensuite, l'installation de PHP-FPM est très aisée, car il n'y a pas de modules pour cette dernière.
Exécutez la commande `dnf install php-fpm -y`

## Configuration primaire du site web

Avant d'avoir un serveur web parfaitement fonctionnel et sécurisé avec l'HTTPS, on va d'abord poser la base du serveur
web sur lequel nous allons héberger notre site. Dans notre exemple, il s'agit du site du serveur GTA RP Les Rescapés RP.

Dans les systèmes Linux sous base Debian, les configurations des sites et des hôtes virtuels d'Nginx sont localisés dans `/etc/nginx/sites-available`,
et afin de rendre les sites dont leurs configurations sont dans ce répertoire, par convention, un lien symbolique vers le fichier de configuration
du site est fait dans `/etc/nginx/sites-enabled`. Un lien symbolique s'utilise de cette façon : 
`ln -s /etc/nginx/sites-available/kiwindows.fr /etc/nginx/sites-enabled/kiwindows.fr`

Cependant, dans les bases Rhel (RedHat), ces deux répertoires n'existent pas par défaut, il faut donc d'abord créer un répertoire similaire puis
configurer Nginx de manière à ce qu'il prenne en compte les fichiers de configuration contenus dans ce nouveau répertoire.

Comme nous allons travailler à présent essentiellement dans le répertoire d'Nginx, on va changer notre terminal de répertoire courant :
`cd /etc/nginx`. Ensuite, on va créer ce nouveau répertoire que l'on va nommer "sites" avec la commande `mkdir sites`. Note : si la commande a
échouée car vous n'avez pas les permissions, vous devez alors l'exécuter avec les permissions de super-utilisateur. Petit raccourci sous Linux,
afin de vous épargner de retaper manuellement la commande, entrez `sudo !!` dans le terminal pour lancer la précédente commande exécutée avec sudo.
Sous les systèmes Linux, `!!` représente la dernière commande exécutée, donc dans notre exemple, elle viens à faire `sudo mkdir sites` dans le cas où `mkdir sites`
a échoué auparavant.

Ensuite, nous allons modifier le fichier de configuration générale d'Nginx, afin qu'il puisse prendre en compte les fichiers de configuration qui seront dans ce nouveau dossier.
Pour cela, nous allons éditer son fichier de configuration avec l'éditeur Nano, qui est un éditeur en ligne de commande sous Linux facile à utiliser, notamment pour les novices.
Lancez alors la commande `nano nginx.conf` dans le cas où vous êtes connecté à l'utilisateur root, ou `sudo nano nginx.conf` dans le cas où vous êtes connecté à un utilisateur
non-root mais ayant les permissions sudo.

Enfin, dans le fichier de configuration, descendre plus bas jusqu'à trouver le blox "http", puis ajouter la ligne `include /etc/nginx/sites/*;`. N'oubliez pas la tabulation,
comme suit sur l'image ci-dessous :

<span style="text-align: center;">![](images/conf_nginx.png)</span>