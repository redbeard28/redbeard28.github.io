---
title:  "Howto: KKMOON RFID Access"
date:   2017-07-27 00:05:00
categories: [Linux]
tags: [Domotique,Securité,Portier]
tipue_search_active: true
---

Petit pense-bête pour la configuration de ce boitier

![SL100 / AD2000](https://images-na.ssl-images-amazon.com/images/I/71tQCaB0CwL._SL1000_.jpg)


**Source provenant d'un commentaire sur amazon.fr**


## Comment brancher AD2000-M
Pour ceux qui comme moi ne parle pas anglais
Sur le bornier vous avez 8 vis :
L’alimentation qui vient de votre transfo 230/12v (entré) :
Rep 1(+12dc) : le + 12V
Rep 2 (GND) : le – du 12V
Sortie des Contacts :
Vous avez 3 possibilités : 1 contact 12v ou 1 contact 5V ou 1 contact direct
Vous devez choisir comment vous voulez commander votre appareil qui sera branché en sortie.
Sur la platine vous avez un petit cavalier Rep J1
Soit vous le mettez en NC (contact fermé) ou NO (contact ouvert)
Rep 3 (Push 1) : c’est le commun (ou neutre) de tous les contacts
Rep 4(lock) : sortie +12v
Donc si vous voulez commander un appareil (gâche électrique ou autre) qui fonctionne en 12v
Branchement Rep 3 et 4
Rep 5 (open in) : sortie +5V
Donc si vous voulez commander un appareil qui fonctionne en 5v, Branchement Rep 3 et 5
Rep 6 (open door) : sortie contact direct , Branchement Rep 3 et 6
(C’est ce qu’il me fallait pour remplacer un bouton poussoir par AD2000-M )
Rep 7 et 8 ; c’est un contact direct (pour votre sonnette) qui est commandé lorsque vous appuyez sur Door Bell (en bleu) sur la façade du AD2000-M

## Maintenant un peu de mise en service …….
*Le mot de passe system (MDPS) est 12345 par defaut, vous pouvez le changer :
Taper # ,taper 12345,taper 1,taper votre nouveau MDPS(expl. 00001)
Pour revenir en mode usine(12345) RESET c’est simple
Débrancher l’alimentation Rep 1(+12V)
Mettre le petit cavalier Rep S16 qui se trouve sur la platine sur position 2 et 3
Rebrancher le Rep1 (+12v) =>alarme et toutes les LEDs clignotentent
Débrancher l’alimentation Rep 1(+12V)
Remettre le petit cavalier Rep S16 qui se trouve sur la platine sur position 1 et 2
Débrancher l’alimentation Rep 1(+12V)
Ok MDPS est de nouveau 12345

## Pour activer les RFID (moi j’appelle ça des badges c’est plus simple)
Il faut identifier les badges, j’en ai 10, donc le premier son ID j’ai mis 0001, le deuxième ID 0002 ect…)
Il faut mettre 4 chiffres pour ID badge
On prend le badge ID 0001 :
Taper # , taper 12345 (c’est le MDPS si vous ne l’avez pas changé)) , taper 2 , taper 0001 (ID badge) , approché votre badge sur l’ AD2000-M pour qu’il le détecte, et taper 6
Voilà, faite pareil avec les autres badges mais surtout n’oublié pas de modifier ID du badge (0001, 0002,0003 ect)
PS : moi, il m’est arrivé que certain badge n’était pas détecté, donc je débranchai le +12v et rebranchai, et je refaisais la manip.

## Comment rentrer un code utilisateur sur le pavé numérique
*es ID utilisateurs (4 chiffres) sont à mettre à la suite des ID Badges
Exemple pour moi, j’ai mis :
  ID (Père) :0011
  ID (mère) : 0012
  ID (enfant 1) : 0013
  ID (enfant 2) : 0014
Taper #, taper le MDPS 12345, taper 2, taper ID utilisateur 0011, taper le code à 6 chiffres que vous aurez choisi et taper 6
Pour ma part, chaque personne de ma famille à son code.



