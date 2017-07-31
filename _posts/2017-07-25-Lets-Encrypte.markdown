---
title:  "Howto: Install Let's Encrypte"
date:   2017-07-27 20:00:00
categories: [Linux]
tags: [Linux,redhat,ssl,apache]
tipue_search_active: true
---

Mise en place de Let's Encrypte pour la mise en place d'un reverse proxy sous Apache2 pour notre projet IoT ThingsBoard.


## Mise à jour apt et installation de GIT
<pre class="prettyprint">
sudo apt-get update
sudo apt-get install git
</pre>

## Cloner le client Let's Encrypt depuis le dépôt Github officiel.
Placer le client dans le dossier /opt de votre serveur.
<pre class="prettyprint">
sudo git clone https://github.com/letsencrypt/letsencrypt /opt/letsencrypt --depth=1
cd /opt/letsencrypt
</pre>

## Mise en place du certificat

Let's Encrypt dispose d'un mode automatique qui va installer les dépendances nécessaires à l'outil et mettre en place les certificats en fonction de votre configuration serveur. Cette installation automatique fonctionne dans le cadre d'un serveur Web apache
<pre class="prettyprint">
/opt/letsencrypt/letsencrypt-auto --apache -d mondomaine.fr -d www.mondomaine.fr
</pre>

Les certificats générés, ainsi que les clés privées sont stockés dans le dossier **/etc/letsencrypt/live/**

Let's Encrypt modifie vos virtual hosts apache en indiquant le chemin vers votre clé privée et votre certificat


## Le renouvellement

Les certificats proposés par Let's Encrypt sont valables pour une durée de 90 jours. Il faudra donc penser à les renouveler avant la fin de cette période. Pour cela on peut utiliser la commande :
<pre class="prettyprint">
/opt/letsencrypt/letsencrypt-auto --apache --renew-by-default  -d mondomaine.fr -d www.mondomaine.fr
</pre>


## Renouvellement automatisé
Une âme charitable a pensé à nous. En effet, un script bash a été partagé par **Erika Heidi** afin de le lancer automatiquement via la crontab.

<pre class="prettyprint">
sudo install -y bc
sudo curl -L -o /usr/local/sbin/le-renew http://do.co/le-renew
sudo chmod +x /usr/local/sbin/le-renew

sudo le-renew domaine.fr
</pre>

Crontab: Lancement toutes les semaines à 01h00 du matin [Crontab GURU](https://crontab.guru/#0_1_*_*_1)
<pre class="prettyprint">
sudo crontab -e
0 1 * * 1 /usr/local/sbin/le-renew domaine.fr >> /var/log/le-renew.log
</pre>