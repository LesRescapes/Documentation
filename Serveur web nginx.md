# Serveur web Nginx
- [Serveur web Nginx](#serveur-web-nginx)
  - [Préface](#préface)
  - [Mise en place du système](#mise-en-place-du-système)
  - [Installation de NGINX et de PHP-FPM](#installation-de-nginx-et-de-php-fpm)
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

