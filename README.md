# Projet Barbapapa - Carte électronique

Le Projet Barbapapa est un projet surprise qui réunit des anciens JINs.

L'une des parties de ce projet est un objet intéractif, ce dépôt en contient
la conception électroniques, réalisée avec [KiCad](https://www.kicad.org/).


# Objectifs

La carte électronique doit permettre la détection de tags RFID, le contrôle 
d'un écran LCD, de LEDs RGB, ainsi que l'utilisation d'un haut-parleur.

Elle doit permettre une opération connectée à un ordinateur, comme indépendante.


# État

La carte est en période de révision: elle n'a pas été produite ou testée.

La versionnement de forme X.Y se lit comme suit:
 - X : Version mise en production (avec 1 comme première production)
 - Y : Révision suivant la mise en production (remis à 0 quand X augmente)


# Choix

## Fabricant

La carte a été conçue pour être fabriquée par JLCPCB en double couche avec une carte de 0.8mm d'épaisseur. Les marges
du design ont donc été pensées pour leurs capacités de fabrication.  
Le choix du 0.8mm est essentiellement pour faciliter l'atteinte de la bonne impédance pour les pistes D+ et D- de l'USB.  
JLCPCB a aussi le gros avantage d'être très bon marché, en passant en double couche les PCBs devraient coûter à peine
quelques livres.


## Carte microcontrôleur

La teensy 4.1 a été choisie pour la flexibilité de ses interfaces et des bibliothèques disponnibles.  
La carte elle-même est bien trop puissante pour l'application, qui ne nécessite pas la capacité de calcul d'un Cortex-M7,
mais cela réduira les contraintes lors du développement du firmware pour faire tourner les différents éléments.

En particulier, les bibliothèques audio de la teensy sont assez réputées et ont servies pour faire différentes pédales
d'effet, et la présence d'un port microSD déjà intégré sur la carte rend le stockage des animations pour le LCD et des
fichiers audios bien plus simple.


## Alimentation

Une des contraintes de conception était la possibiltié d'utiliser l'objet de façon indépendante d'un ordinateur. Il a donc
fallut intégrer une batterie capable de tenir la charge du système.

L'élément le plus consommateur ici est l'amplificateur audio. Bien que la majeure partie du temps il ne consomme presque rien,
les pics de courant demandés lors du jeu audio ont poussé au choix d'une batterie Li-Ion et d'un régulateur pouvant soutenir
2A de courant continu.

Les batteries Li-Ion ont l'avantage monter à 4.2V à pleine charge, il est donc possible de les charger par USB. Cela évite
l'utilisation de piles non-rechargeables, que je voulais éviter. Elles permettent aussi de débiter un courant suffisant pour
alimenter tout le système, ce qui est rarement le cas des piles jetables de toutes façons.

Le système est donc alimenté par la batterie, parfois complémenté par le chargeur qui fournit jusqu'à 500mA pour la charger.
Seule la Teensy est alimentée directement par la batterie, tout le reste du système est derrière le régulateur 3.3V (tension
logique de la Teensy).

L'alimentation à la Teensy est contrôlée par un interrupteur externe branché sur `SW2`, puis cette dernière contrôle l'activation
du régulateur 3.3V. Elle peut lire la tension fournie par la batterie et décider de couper le système si nécessaire pour la
préserver.  


### À noter

Le gros du système est protégé d'une inversion de polarité de la batterie Li-Ion par une diode. Cependant, le chargeur lui-même
ne l'est pas. Cette décision à été prise car l'inversion ne devrait pas être trop commune, le réceptacle étant polarisé et indiqué,
mais aussi pour la chute de tension supplémentaire que ça aurait apporté.

***Ne pas mettre la batterie à l'envers !***


## Périphériques

La carte est conçue pour la connexion d'un écran LCD de Nokia 5110 pour son style à gros pixels et d'un module NFC.  

Le module NFC choisi est le RC522 pour sa capacité à gérer de multiples tags dans sa zone de détection et ainsi permettre de
poser plusieurs figurines sur la zone en même temps.  

Au cas où la zone de détection s'avère trop faible ou que la connexion du module NFC et du module LCD au même bus SPI soit
problématique, deux alternatives ont été prévues sur la carte :  
 1. La possibilité d'utiliser du soft-SPI pour le LCD
 2. La sortie d'un deuxième bus SPI indépendant pour un deuxième module NFC en parallèle

L'amplificateur a été choisi pour son entrée I2S (signal audio numérique) et son circuit d'amplification très complet et
auto-suffisant, ne nécessitant pas grand chose de plus que quelques condensateurs et résistances pour le choix de fonctionnement.  
C'est un MAX98357A, il existe aussi sous forme de module entre autre par Sparkfun comme "I2S Audio Breakout".  

Deux autres séries de broches sont présentes : une pour brancher un câble FTDI et avoir accès à la série de la Teensy directement
(seules les broches TX/RX et la masse sont connectées) et un trois broches pour contrôler des LEDs RGB addressables style neopixel.



# License

Les éléments présents dans ce dépôt sont Open Source Hardware, sous license [CERN Open Hardware Licence Version 2 - Weakly Reciprocal](https://ohwr.org/cern_ohl_w_v2.txt).

# Crédits

Carte conçue par Téo-CD, avec les conseiles de Victor Védie, François Charles, Lucas Molia.

