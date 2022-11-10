# Préambule

Ce document a été rédigé par des étudiants de P19. 

Il décrit comment manipuler le tissu connecté **eTextile E256** au niveau programmatoire.

# Compiler le code du eTextile

***ATTENTION : cette étape n'est nécessaire que si le eTextile n'a pas été configuré. Il est recommandé de tester le eTextile avant d'essayer de compiler le code du tissu.***

## Prérequis

- Cloner [le projet eTextile modifié](https://github.com/vu-alex-enjmin/eTextile)
- Installer [Arduino 1.8](https://www.arduino.cc/en/software)
- Installer Teensyduino ([Windows](https://www.pjrc.com/teensy/td_157/TeensyduinoInstall.exe), [Autres](https://www.pjrc.com/teensy/td_download.html))
- Installer [Bounce2](https://github.com/thomasfredericks/Bounce2)
- Installer [OSC](https://github.com/CNMAT/OSC)

Des informations pour installer des bibliothèques Arduino sont disponibles [ici](https://docs.arduino.cc/software/ide-v1/tutorials/installing-libraries).

## Compiler le projet

Pour compiler le projet, il faudra que vous ayez installé les différents éléments de la section précédente.

1. Allumer Arduino 1.8 et ouvrir le projet `Firmware/main/main.ino`
2. Brancher le eTextile et sélectionner le port COM correspondant
3. Vérifier que vous êtes en mode Serial (`USB Type: "Serial"`)
4. Vérifier que le code compile bien
5. Téléverser le programme vers le eTextile

## Autres informations

Le fichier `config.h` indique au projet ce que le tissu doit sortir comme output (grâce à un sytème de `#define`).

Le projet original peut être retrouvé [ici](https://github.com/eTextile/Matrix/tree/master/Firmware).

# Récupérer les données du eTextile

## Configuration

Si le tissu est correctement configuré, il devrait émettre des données **Serial**.

Plusieurs infos sont à prendre en compte pour récupérer les données du eTextile :
- Sur Windows, le port est généralement sous la forme `COM[0-9]` (ex : `COM4`, `COM6`...)
- La vitesse de transmission par défaut est de `230400` bauds (bits/seconde)

## Format des données

Par défaut, le tissu envoie ses données sous forme de blobs. Un blob correspond à un point de contact, plus ou moins grand.

Le format des données est le suivant :
```
<DEBUG_FIND_BLOBS> <LS> <S> <X> <Y> <W> <H> <D>
```
Avec : 
- `DEBUG_FIND_BLOBS` : identifiant unique au blob, qui permet notamment de traquer les déplacements du blob
- `LS` : état précédent du blob (1 si présent, 0 si non présent) 
- `S` : état actuel du blob (1 si encore présent, 0 si plus présent)
- `X` : coordonnées X du blob sur le eTextile, par défaut entre 0 et 58
- `Y` : coordonnées Y du blob sur le eTextile, par défaut entre 0 et 58
- `W` : largeur du blob (axe X)
- `H` : hauteur du blob (axe Y)
- `D` : profondeur du blob (axe Z) (dépend de la pression exercée)

Le séparateur entre les données est une *tabulation* (`\t`).

Exemple d'output possible :
```
DEBUG_FIND_BLOBS:2   LS:1   S:1   X:55.599998   Y:28.922581   W:8   H:26   D:10	
```

## Problèmes probables et solutions possibles

- Il est possible que le tissu renvoie des faux positifs. Pour remédier à cela, vous pouvez essayer de bien mettre à plat le tissu et/ou ajouter un seuil minimal de pression à prendre en compte dans votre code.
-  Le sens des axes peuvent ne pas vous aller. Vous pouvez les inverser par un petit calcul (ex : pour inverser l'axe Y, on peut faire : `newY = 58 - oldY`).
- Il y a probablement un décalage dans l'axe X. Pour remédier à cela, vous pouvez ajouter un offset modulo 58 à la valeur que vous renvoie le tissu. Pour information, le décalage qui a le mieux fonctionné pour nous est de `29.13`.
- S'il vous faut un seul point de contact, vous pouvez effectuer une moyenne pondérée de tous les blobs dans votre code.


***PS : Good luck avec le eTextile, et bon courage pour votre projet !***