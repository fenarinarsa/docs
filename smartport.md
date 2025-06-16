# Mini doc Smartport

Ces infos proviennent de http://apple2.guidero.us/doku.php/articles/iicplus_smartport_secrets  
Il y a aussi https://www.bigmessowires.com/2015/06/11/apple-ii-hard-disk-emulation/

Les cartes Smartport sont apparemment en slot 5 de façon standard mais ça n'est pas une obligation.

## Format

Les "blocks" font tous 512 octets sur disque.

- Format disquette (.po)  
1 secteur = 512 octets x le nombre de secteurs de la disquette.  
  1600 blocks pour une disquette 3"1/2, 280 pour une disquette 5"1/4.  
- Format disque dur (.hdv)  
À voir si c'est vraiment différent, apparemment il y a juste un entête en plus avec le nombre de blocks.  

## Boot Unidisk

S'active soit en premier, soit s'il n'y a pas de disquette en `$C600`, selon la ROM.  
Charge les premiers blocks (de 512 octets) en `$800` (comme le Disk II)  
Au moment d'appeler la routine du boot block, donne le numéro du DEVICE actif dans `X` sous la forme `$x0`  (par ex lecteur 1 en slot 5 : `X=$50`)  

Format du bootblock, identique à celui du boot secteur Disk II:  
`$800` => nombre de blocks à charger au boot (je ne sais pas si c'est vraiment utilisé)  
`$801` => code  

## Appel Smartport

### Structure de paramètres en $42  

`$42` Commande Smartport (ex. $01)  
`$43` Device Smartport (ex. $50)  
`$44+$45` adresse RAM destination (pour read/write)  
`$46+$47` numéro du block à lire/écrire (pour read/write)

### Appel ROM

`JSR $Cx0A`  

Retour:  
`A` = error code (`$00` = pas d'erreur)  
`X et Y` = ça dépend de l'appel  

### Commandes Smartport

`$00 STATUS` Renvoie le nombre de blocks du device dans `X` et `Y` (octet poids faible dans `X`) + des flags?  
`$01 READ` Lit un block  
`$02 WRITE` Écrit un block  
`$03 FORMAT` Formate TOUT le disque  
`$40 Extended STATUS`   

DIX n'utilise que la commande READ :p mais les trucs genre Total Replay ou ProDOS demandent sans doute une simulation plus complète.  

Différence avec la vraie ROM :  
Notamment, les screen holes en `$400-$7FF` sont utilisés (aucune idée de ce qu'il en fait).  
Le chargement prend un peu de temps, alors qu'en simulé c'est immédiat.  

