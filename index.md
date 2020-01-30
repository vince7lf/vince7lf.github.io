# Préparation: système d'alimentation, micro-sd, image

* La première étape est d'avoir un système d'alimentation assez puissant: un adapteur USB fournissant un peu plus que 5V, et d'au moins 2.4amp. 
* La seconde étape est de préparer Jetpack sur une microsd. Jetpack est un SDK, une boite à outil, préparée par Nvidia, qui contient L4T, le système d'exploitation Ubuntu 18 de NVidia, et des outils nécessaires pour le deeplearning et l'inférence.

La référence pour la mise en place de la micro-sd se trouve sur le site suivant, et peut se résumer ainsi:
https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit

* Télécharger l'image nv-jetson-nano-sd-card-image-r32.3.1.zip (~5.4 Gb)
* Télécharger et exécuter le Sd Card Formatter: quick format; pas de label
* Télécharger et exécuter Etcher pour flasher la micro-sdo avec l'image, ce qui prend entre 5 et 10 minutes selon le PC utilisé

# Démarrage du système pour la 1er fois
Au  1er démarrage, le système nécessite de recevoir certaines informations pour l'installation, tel que le language du système et du clavier, le fuseau horaire, l'espace de la micro-sd qu'il peut utiliser. 

* language : anglais
* usager: lefv2603; c'est mon nom d'usager à l'université de Sherbrooke. 
* mot de passe: identique au nom d'usager
* fuseau horaire : New-York

Connecter le réseau wifi. 

# Mise en place de Jetpack avec projet "Hello AI World"
La référence de l'installation est située à cette page:
https://github.com/dusty-nv/jetson-inference/blob/master/docs/building-repo-2.md

En gros, les étapes sont:
* Cloning the Repo


