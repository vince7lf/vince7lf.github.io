#Préparation: système d'alimentation, micro-sd, image

* La première étape est d'avoir un système d'alimentation assez puissant: un adapteur USB fournissant un peu plus que 5V, et d'au moins 2.4amp. 
* La seconde étape est de préparer Jetpack sur une microsd. Jetpack est une boite à outil préparé par Nvidia, qui contient L4T, le système d'exploitation Ubuntu 18 de NVidia, et des outils nécessaires pour le deeplearning et l'inférence.

La référence pour la mise en place de la micro-sd se trouve sur le site suivant:
https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit

* Télécharger l'image nv-jetson-nano-sd-card-image-r32.3.1.zip (~5.4 Gb)
* Télécharger et exécuter le Sd Card Formatter: quick format; pas de label
* Télécharger et exécuter Etcher pour flasher la micro-sdo avec l'image 

