# Préparation du Jetson nano

1. TOC
{:toc}

## Montage, système d'alimentation, micro-sd, image
* La première étape est le montage du Jetson nano. Pour des informations pertinentes sur la composition du Jetson nano, se référer au [JETSON NANO DEVELOPER KIT User Guide]( https://developer.download.nvidia.com/assets/embedded/secure/jetson/Nano/docs/NVIDIA_Jetson_Nano_Developer_Kit_User_Guide.pdf?4i_bSgtK7-9jHyrfcxqONSTGdsqaUzN66jUD0MSJOEDHHZFrBeQo0tVlgVGftxj3E9lYDGVrnG7Q1dSgrYy_2qZmOeccW7Br4Hxw8bygQ6VjpFL0sPO2ogMhJuTcQARio68xNvqYSWHChfIJLSZH04TAcJicfE7QbRTHhiwpomBVNla-QTt-ItqM5sFpoobABNEJ7lZS)

* La seconde étape est d'avoir un système d'alimentation assez puissant: un adapteur USB fournissant un peu plus que 5V, et d'au moins 4amp. 
* La troisième étape est de préparer Jetpack sur une microsd. Jetpack est un SDK, une boite à outil, préparée par Nvidia, qui contient L4T, le système d'exploitation Ubuntu 18 de NVidia, et des outils nécessaires pour le deeplearning et l'inférence.

La référence pour la mise en place de la micro-sd se trouve sur le site suivant, et peut se résumer ainsi:
<https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit>

* Télécharger l'image nv-jetson-nano-sd-card-image-r32.3.1.zip (~5.4 Gb)
* Télécharger et exécuter le Sd Card Formatter: quick format; pas de label
* Télécharger et exécuter Etcher pour flasher la micro-sdo avec l'image, ce qui prend entre 5 et 10 minutes selon le PC utilisé

> Notes: 
> - La carte micro-sd 64Gb EVO Plus *rouge* m'appartient personnellement. 
> - La carte micro-sd 64Gb EVO Select *verte* appartient au projet

## Démarrage du système pour la 1er fois
Au  1er démarrage, le système nécessite de recevoir certaines informations pour l'installation, tel que le langage du système et du clavier, le fuseau horaire, l'espace de la micro-sd qu'il peut utiliser. Suivre le Wizard.

* langage : anglais
* usager: lefv2603; c'est mon nom d'usager à l'université de Sherbrooke. 
* mot de passe: identique au nom d'usager
* fuseau horaire : New-York

Connecter le réseau wifi via l'adapteur usb s'il n'y a pas d'extension interne m.2 installée. 

## test des performances de la carte micro-sd (ou autre disque, comme un SSD Sata ou Nvme, via PCIe ou USB)
Pour tester les performances de la micro-sd ou d<un disk interne ou externe, connecté via USB3.0, PCI2 ou carte extension M.2, l'utilitaire hdparm peut etre facilement utilise. 

Référence : https://devtalk.nvidia.com/default/topic/1049706/jetson-nano/can-this-board-use-m-2-ssd-nvme/

```
$ sudo apt install hdparm
$ sudo hdparm -t --direct /dev/sda
/dev/sda:
Timing O_DIRECT disk reads: 1004 MB in 3.00 seconds = 334.15 MB/sec
```

## Installation d'un SSD interne Nvme M.2 avec carte d'extension M.2 vers USB3.0
À noter qu'à ce jour le disque n'arrive pas resté connecté très longtemps, pour une raison inconnue, il devient indisponible.
Référence : https://www.jetsonhacks.com/2019/09/17/jetson-nano-run-from-usb-drive/


## Mise en place de Jetpack avec le projet "Hello AI World"
La référence de l'installation est située à cette page:
<https://github.com/dusty-nv/jetson-inference/blob/master/docs/building-repo-2.md>

En gros, les étapes sont:
* Cloner le projet
* Installer les libraires Python
* Configurer le build des modèles avec cmake; le répertoire ~/projects/dusty-nv/jetson-inference/build a été créer pour contenir les modèles. 
* Télécharger les modèles; uniquement les modlèles des segmentations sémentiques sont téléchargés. Cela prend une bonne demie-heure, selon la connexion Internet (wifi vs Ethernet, bande passance)
* Installer PyTorch; nécessaire pour faire le transfer learning; uniquement la version PyTorch v1.1.0 pour Python 3.6 est installée. 
* Compiler le projet; cette étape prend 3-5 minutes
```
$ cd jetson-inference/build
$ make
$ sudo make install
$ sudo ldconfig
```

## Premièr test d'inférence avec un modèle de segmentation sémantique
Ce test permet de savoir si le système est bien en place, et si le Jetson nano est desservi par assez d'énergie. Sinon, il s'éteind tout simplement pendant l'exécution de l'inférence. 

La commande utilisée est:
```
$ cd ~/projects/dusty-nv/jetson-inference
$ cd ./build/aarch64/bin
$ ./segnet-console --network=fcn-resnet18-deepscene --visualize=mask --alpha=10000 images/city_0.jpg output.jpg
```
La première exécution le rapport donne des valeurs assez élevées. 
```
[TRT]   ------------------------------------------------
[TRT]   Timing Report networks/FCN-ResNet18-Cityscapes-512x256/fcn_resnet18.onnx
[TRT]   ------------------------------------------------
[TRT]   Pre-Process   CPU   2.78468ms  CUDA   3.04458ms
[TRT]   Network       CPU  59.17971ms  CUDA  58.76735ms
[TRT]   Post-Process  CPU   1.05956ms  CUDA   1.05630ms
[TRT]   Total         CPU  63.02396ms  CUDA  62.86823ms
[TRT]   ------------------------------------------------
```
Les fois suivantes, les valeurs du rapport restent stables. Par contre la commande `sudo jetson_clocks` a été exécuté après la première fois. 
```
[TRT]   ------------------------------------------------
[TRT]   Timing Report networks/FCN-ResNet18-Cityscapes-512x256/fcn_resnet18.onnx
[TRT]   ------------------------------------------------
[TRT]   Pre-Process   CPU   0.08807ms  CUDA   0.61323ms
[TRT]   Network       CPU  28.77813ms  CUDA  28.67969ms
[TRT]   Post-Process  CPU   0.63754ms  CUDA   0.20552ms
[TRT]   Total         CPU  29.50374ms  CUDA  29.49844ms
[TRT]   ------------------------------------------------
```

## Test de la caméra Raspberry Pi v2
Réference : <https://www.jetsonhacks.com/2019/04/02/jetson-nano-raspberry-pi-camera/>

La première chose a s'assurer est d'installer la caméra dans le boitier à la bonne position, c'est à dire la bonne orientation. Le cable doit longer le haut du boitier. 

La commande pour tester la caméra est la suivante:
```
$ gst-launch-1.0 nvarguscamerasrc ! 'video/x-raw(memory:NVMM),width=3820, height=2464, framerate=21/1, format=NV12' ! nvvidconv flip-method=0 ! 'video/x-raw,width=960, height=616' ! nvvidconv ! nvegltransform ! nveglglessink -e
```
Une nouvelle fenêtre apparait avec la vidéo de la caméra. 
A noter que le nombre de frame est 21/1 dans l'exemple. Pour augmenter le nombre de frame, il faut diminuer la résolution. Voir plus bas. 

Pour les détails de la ligne de dommande et l'utilisation de GStreamer, il faut se référer au guide suivant. GStreamer est l'outil qui est préconisé par NVidia (vs ffmpeg) afin de profiter des GPUs.  
<https://developer.download.nvidia.com/embedded/L4T/r32_Release_v1.0/Docs/Accelerated_GStreamer_User_Guide.pdf>

### Résolutions x FPS supportés par la caméra
Référence: <https://github.com/dusty-nv/jetson-inference/blob/master/docs/segnet-camera-2.md>

Installer v4l-utils:
```
$ sudo apt-get install v4l-utils
```
Et exécuter l'utilitaire *v4l2-ctl*:
```
$ v4l2-ctl --list-formats-ext
ioctl: VIDIOC_ENUM_FMT
	Index       : 0
	Type        : Video Capture
	Pixel Format: 'RG10'
	Name        : 10-bit Bayer RGRG/GBGB
		Size: Discrete 3264x2464
			Interval: Discrete 0.048s (21.000 fps)
		Size: Discrete 3264x1848
			Interval: Discrete 0.036s (28.000 fps)
		Size: Discrete 1920x1080
			Interval: Discrete 0.033s (30.000 fps)
		Size: Discrete 1280x720
			Interval: Discrete 0.017s (60.000 fps)
		Size: Discrete 1280x720
			Interval: Discrete 0.017s (60.000 fps)
```
On peut s'appercevoir que la caméra supporte jusqu'à 60 images-par-seconde en résolution 1280x720, et 21 images-par-seconde en résolution 3264x2464. 

Essayons le format 60FPS 1280x720: 
```
$ gst-launch-1.0 nvarguscamerasrc ! 'video/x-raw(memory:NVMM),width=1280, height=720, framerate=60/1, format=NV12' ! nvvidconv flip-method=0 ! 'video/x-raw,width=960, height=616' ! nvvidconv ! nvegltransform ! nveglglessink -e
```
Cela fontionne. 

### Nano hardware encoder decoder
Basé sur le commentaire trouvé sur le forum devtalk (<https://devtalk.nvidia.com/default/topic/1050950/jetson-nano/h-264-h-265-encoding-using-jetson-nano-gpu/>), il est recommandé d'utiliser GStreamer, à la place du populaire ffmpeg, pour bénéficier de l'accélération des GPUs du nano. ffmpeg utilise les CPUs et non les GPUs. 

*"ffmpeg doesn't use the Nano's hardware encoder or decoder, you can run it but it will be CPU-only."*

La référence à utiliser pour traiter les images et vidéos avec le Jetson nano est: 
<https://developer.download.nvidia.com/embedded/L4T/r32_Release_v1.0/Docs/Accelerated_GStreamer_User_Guide.pdf>

## Test d'inférence segmentation sémantique en temps réel avec la caméra

NVidia fournit déjà des tests pour réaliser l'inférence de modèle de segmentation sémentique en temps réel avec la caméra. 

Référence: <https://github.com/dusty-nv/jetson-inference/blob/master/docs/segnet-camera-2.md>

Par exemple:
```
$ cd ~/projects/dusty-nv/jetson-inference
$ cd ./build/aarch64/bin
$ ./segnet-camera.py --network=fcn-resnet18-mhp
```
> **_NOTE Importante:_**
> Il y a un fix a apporter avant de procéder. Le paramètre *flip-method* n'a pas la bonne valeur. Il doit être à **`0`** pour être conforme à la bonne orientation de la caméra dans le boitier.

* Ouvrir le fichier ./utils/camera/gstCamera.cpp du projet jetson-inference
```
$ cd ~/projects/dusty-nv/jetson-inference
$ vim ./utils/camera/gstCamera.cpp
```
* faire une recherche pour "flip-method = 2"
* modifier la valeur pour 0 ("flip-method = 0")
* sauvegarder le fichier
* recompiler le projet (voir section plus haut)
```
$ cd jetson-inference/build
$ make
$ sudo make install
$ sudo ldconfig
```
* ré-exécuter le test d'inférence de segmentation sémantique en temps réel avec la caméra
```
$ cd ~/projects/dusty-nv/jetson-inference
$ cd ./build/aarch64/bin
$ ./segnet-camera.py --network=fcn-resnet18-mhp
```

## Test d'inférence segmentation sémantique d'une vidéo
Références: https://github.com/umlaeute/v4l2loopback
https://unix.stackexchange.com/questions/5452/manipulating-dev-video
https://sourceforge.net/projects/v4l2vd/
