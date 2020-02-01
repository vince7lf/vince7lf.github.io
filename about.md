# À propos de

L’université de Sherbrooke participe a un projet avec la compagnie "LES PONTS JACQUES CARTIER ET CHAMPLAIN INCORPORÉE (PJCCI)" afin de les aider à évaluer la possibilité de garder ouverte, l’hiver, la piste multifonctionnelle du pont Jacques-Cartier.

L’université leur propose de mettre en place une plateforme constituée de capteurs intelligents installés sur des nano-ordinateurs. Parmi les capteurs, une caméra prendra des vidéos ou des photos de très hautes résolutions de la même cible de diverses vues. Les images seront interprétées par un module d’apprentissage profond adapté à ce besoin, et qui permettra d’identifier l’état de la piste en temps réel. 

L’essai se limitera à tester les performances et les limites d’un nano-ordinateur avec l’inférence d’un modèle d’un réseau de neurones à convolution entier pour la segmentation sémantique en temps réel de la piste multifonctionnelle, apporter des optimisations si nécessaire.

## Problématiques 
* Défi pour le domaine du transport actif et durable d’être soutenu par des solutions technologiques fiables (opérationnelles) pour pouvoir offrir des services de qualité et sécuritaire sur l’ensemble des quatre saisons.
* Capacités d’un nano-ordinateur de segmenter sémantiquement, en temps réel, des vidéos de hautes résolutions et de fréquence d’images par seconde (FPS) élevée.
* Adapter en temps réel le modèle d’apprentissage profond avec de nouvelles données directement avec le nano-ordinateur.

## Objectifs
Évaluer la capacité d’un nano-ordinateur à inférer, en temps réel, un modèle d’apprentissage profond permettant la segmentation sémantique d’une piste multifonctionnelle.
La segmentation sera limitée à deux classes : une classe pour la piste et une autre pour les autres éléments présents dans l’image.
### Sous-objectifs:
* Évaluer les limites de la plateforme, matérielle et applicative
* Trouver des moyens d’optimiser la plateforme, au besoin, d’un point de vue matériel, mais aussi applicatif
* Documenter l’approche, les tests, et les résultats
* Permettre un accès à distance sécurisé au nano-ordinateur
