# Serveur web Nginx
## Préface
[Nginx](https://fr.wikipedia.org/wiki/NGINX) est un serveur web très populaire dans le domaine du cloud pour sa meilleure
gestion des fortes charges et pour sa configuration très modulable (il est par
ailleurs très bien optimisé pour du [reverse-proxy](https://fr.wikipedia.org/wiki/Proxy_inverse)).  
PHP-FPM est le module permettant au serveur web Nginx d'exécuter des pages codées en PHP.

[Acme.sh](https://acme.sh) est un script permettant d'obtenir des certificats pour des serveurs web en exploitant le protocole acme.

Le [CAA](https://fr.wikipedia.org/wiki/DNS_Certification_Authority_Authorization) est un type d'enregistrement DNS
permettant à ces derniers de lister les Autorités de Certification (CA) pouvant délivrer un certificat au nom de domaine correspondant.
## Table des matières
1. Installation de NGINX et de PHP-FPM
2. Configuration primaire du site web
3. Récupération des clefs d'API OVH
4. Acme.sh
    1. Installation
    2. Enregistrement auprès de l'autorité de certification
    3. Génération des certificats
5. Configuration définitive du site web
6. Configuration définitive d'NGINX
7. Configuration du CAA