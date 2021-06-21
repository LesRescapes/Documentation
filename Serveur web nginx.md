# Serveur web Nginx
## Préface
[Nginx](https://fr.wikipedia.org/wiki/NGINX) est un serveur web très populaire dans le domaine du cloud pour sa meilleure
gestion des fortes charges et pour sa configuration très modulable (il est par
ailleurs très bien optimisé pour du [reverse-proxy](https://fr.wikipedia.org/wiki/Proxy_inverse)).
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