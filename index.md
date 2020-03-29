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
source: <https://developer.nvidia.com/jetson-nano-sd-card-image>
* Télécharger et exécuter le Sd Card Formatter: quick format; pas de label
* Télécharger et exécuter Balena Etcher pour flasher la micro-sdo avec l'image, ce qui prend entre 5 et 10 minutes selon le PC utilisé

> Notes: 
> - La carte micro-sd 64Gb EVO Plus *rouge* m'appartient personnellement. 
> - La carte micro-sd 64Gb EVO Select *verte* appartient au projet
> - La carte micro-sd 32Gb ScanDisk Ultra "blanche" m'appartient personnellement. 

## Démarrage du système pour la 1er fois
Au  1er démarrage, le système nécessite de recevoir certaines informations pour l'installation, tel que le langage du système et du clavier, le fuseau horaire, l'espace de la micro-sd qu'il peut utiliser. Suivre le Wizard.

* langage : anglais
* usager: lefv2603; c'est mon nom d'usager à l'université de Sherbrooke. 
* mot de passe: identique au nom d'usager
* fuseau horaire : New-York

Connecter le réseau wifi via l'adapteur usb s'il n'y a pas d'extension interne m.2 installée. 

## Test des performances de la carte micro-sd (ou autre disque, comme un SSD Sata ou Nvme, via PCIe ou USB)
Pour tester les performances de la micro-sd ou d'un disque interne ou externe, connecté via USB3.0, PCI2 ou carte extension M.2, l'utilitaire hdparm peut être facilement utilisé. 

Référence: <https://devtalk.nvidia.com/default/topic/1049706/jetson-nano/can-this-board-use-m-2-ssd-nvme/>

```
$ sudo apt install hdparm
```
SSD disk
```
$ sudo hdparm -t --direct /dev/sda
/dev/sda:
Timing O_DIRECT disk reads: 1004 MB in 3.00 seconds = 334.15 MB/sec
```
Microsd card (regular)
```
lefv2603@lefv2603-desktop:~$ sudo hdparm -t --direct /dev/mmcblk0p1

/dev/mmcblk0p1:
 Timing O_DIRECT disk reads: 122 MB in  3.03 seconds =  40.22 MB/sec
```

## Installation d'un SSD interne Nvme M.2 avec carte d'extension M.2 vers USB3.0
À noter qu'à ce jour le disque n'arrive pas resté connecté très longtemps, pour une raison inconnue, il devient indisponible.

Référence: <https://www.jetsonhacks.com/2019/09/17/jetson-nano-run-from-usb-drive/>

## Mise en place de Jetpack avec le projet "Hello AI World"
La référence de l'installation est située à cette page:
<https://github.com/dusty-nv/jetson-inference/blob/master/docs/building-repo-2.md>

En gros, les étapes sont:
* Mettre à jour le système
```
$ sudo apt-get update
````
* Mettre à jour les librairies nécessaires
```
$ sudo apt-get install git cmake libpython3-dev python3-numpy
````
* Cloner le projet
```
$ git clone https://github.com/dusty-nv/jetson-inference
$ cd jetson-inference
$ git submodule update --init
```
* Configurer le build des modèles avec cmake; le répertoire ~/projects/dusty-nv/jetson-inference/build a été créer pour contenir les modèles. 
```
$ cd jetson-inference
$ mkdir build
$ cd build
$ cmake ../
```
* Télécharger les modèles; uniquement les modlèles des segmentations sémentiques sont téléchargés. Cela prend une bonne demie-heure, selon la connexion Internet (wifi vs Ethernet, bande passance)
```
$ cd jetson-inference/tools
$ ./download-models.sh
```
* Installer PyTorch; nécessaire pour faire le transfer learning; uniquement la version PyTorch v1.1.0 pour Python 3.6 est installée. 
```
$ cd jetson-inference/build
$ ./install-pytorch.sh
```
* Compiler le projet; cette étape prend 3-5 minutes
```
$ cd jetson-inference/build
$ make
$ make -j$(nproc)
$ sudo make install
$ sudo ldconfig
```

## Mise à jour
### pip
Installation de pip pour Python2 
```
sudo apt install python3-pip
```
pip3 pour Python3 devrait être installé avec l'étpae précédente. 

### dconf-editor pour basculer entre les fenêtres, et non les applications (défaut), avec Alt+Tab
L'alternative est de basculer entre applications avec Alt-# (clavier FR) (ou Alt+\` clavier US)  (touche en dessous de Esc)
```
sudo apt-get install dconf-tools
```
Ensuite ouvrer l'editor dconf-editor:
Référence: https://superuser.com/questions/394376/how-to-prevent-gnome-shells-alttab-from-grouping-windows-from-similar-apps
- "Open dconf-editor"
- "Go to org/gnome/desktop/wm/keybindings"
- "Move the value '<Alt>Tab' from switch-applications to switch-windows"
- "Optionally move '<Shift><Alt>Tab' from switch-applications-backward to switch-windows-backward"
- "If you want switch-windows to work across desktops, not just in the current desktop, you can also uncheck org/gnome/shell/window-switcher/current-workspace-only (Courtesy of @CharlBotha)"
- "Close dconf-editor"
- "If using X11, press <Alt>F2, then type r to restart Gnome."

## Premier test d'inférence avec un modèle de segmentation sémantique
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
Réference: <https://www.jetsonhacks.com/2019/04/02/jetson-nano-raspberry-pi-camera/>

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

### Validation des capacités de la caméra

```
$ v4l2-ctl -d 0 --all 
Driver Info (not using libv4l2):
	Driver name   : tegra-video
	Card type     : vi-output, imx219 6-0010
	Bus info      : platform:54080000.vi:0
	Driver version: 4.9.140
	Capabilities  : 0x84200001
		Video Capture
		Streaming
		Extended Pix Format
		Device Capabilities
	Device Caps   : 0x04200001
		Video Capture
		Streaming
		Extended Pix Format
Priority: 2
Video input : 0 (Camera 0: no power)
Format Video Capture:
	Width/Height      : 3264/2464
	Pixel Format      : 'RG10'
	Field             : None
	Bytes per Line    : 6528
	Size Image        : 16084992
	Colorspace        : sRGB
	Transfer Function : Default (maps to sRGB)
	YCbCr/HSV Encoding: Default (maps to ITU-R 601)
	Quantization      : Default (maps to Full Range)
	Flags             : 

Camera Controls

                     group_hold 0x009a2003 (bool)   : default=0 value=0 flags=execute-on-write
                    sensor_mode 0x009a2008 (int64)  : min=0 max=0 step=0 default=0 value=0 flags=slider
                           gain 0x009a2009 (int64)  : min=0 max=0 step=0 default=0 value=16 flags=slider
                       exposure 0x009a200a (int64)  : min=0 max=0 step=0 default=0 value=13 flags=slider
                     frame_rate 0x009a200b (int64)  : min=0 max=0 step=0 default=0 value=2000000 flags=slider
                    bypass_mode 0x009a2064 (intmenu): min=0 max=1 default=0 value=0
                override_enable 0x009a2065 (intmenu): min=0 max=1 default=0 value=0
                   height_align 0x009a2066 (int)    : min=1 max=16 step=1 default=1 value=1
                     size_align 0x009a2067 (intmenu): min=0 max=2 default=0 value=0
               write_isp_format 0x009a2068 (bool)   : default=0 value=0
       sensor_signal_properties 0x009a2069 (u32)    : min=0 max=0 step=0 default=0 flags=read-only, has-payload
        sensor_image_properties 0x009a206a (u32)    : min=0 max=0 step=0 default=0 flags=read-only, has-payload
      sensor_control_properties 0x009a206b (u32)    : min=0 max=0 step=0 default=0 flags=read-only, has-payload
              sensor_dv_timings 0x009a206c (u32)    : min=0 max=0 step=0 default=0 flags=read-only, has-payload
               low_latency_mode 0x009a206d (bool)   : default=0 value=0
                   sensor_modes 0x009a2082 (int)    : min=0 max=30 step=1 default=30 value=5 flags=read-only

```
## Nano hardware encoder decoder
Basé sur le commentaire trouvé sur le forum devtalk (<https://devtalk.nvidia.com/default/topic/1050950/jetson-nano/h-264-h-265-encoding-using-jetson-nano-gpu/>), il est recommandé d'utiliser GStreamer, à la place du populaire ffmpeg, pour bénéficier de l'accélération des GPUs du nano. ffmpeg utilise les CPUs et non les GPUs. 

*"ffmpeg doesn't use the Nano's hardware encoder or decoder, you can run it but it will be CPU-only."*

La référence à utiliser pour traiter les images et vidéos avec le Jetson nano est: 
<https://developer.download.nvidia.com/embedded/L4T/r32_Release_v1.0/Docs/Accelerated_GStreamer_User_Guide.pdf>

### Pour jouer une vidéo avec gstreamer

Cette commande va jouer une vidéo mpeg4 en plein écran (sans le son). Ctrl+C pour arrêter. 
Il est important d'indiquer le chemin complet de la vidéo. 
```
gst-launch-1.0 filesrc location=~/Downloads/1080p.mp4 ! decodebin ! queue ! videoconvert ! autovideosink
```

Pour jouer une vidéo avec une rotation: 
```
lefv2603@lefv2603-jetsonnano:~$ gst-launch-1.0 filesrc location=/media/3433-3732/DCIM/Camera/20200308_150708.mp4 ! decodebin ! queue ! nvvidconv flip-method=3 ! autovideosink
```

### Pour extraire des images de la vidéo

Cette commande permet d'extraire des images JPEG (PNG est aussi possible) d'une vidéo .mp4 prise avec un cellulaire (SamSung S8). La vidéo de l'exemple a une résolution de 1920x1080, 60FPS. Les images ont une résolution similaire (1920x1080) et sont sauvegardées à l'emplacement où la commande est exécuté, dans cet exemple c'est le 'home' directory /home/lefv2603 (~).

```
lefv2603@lefv2603-jetsonnano:~$ gst-launch-1.0 -v filesrc location=/media/3433-3732/DCIM/Camera/20200308_150708.mp4 ! qtdemux ! decodebin ! queue ! nvvidconv flip-method=3 ! videorate ! 'video/x-raw,framerate=1/5' ! nvjpegenc ! multifilesink location=file%d.jpg
```

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
> 
> * Ouvrir le fichier ./utils/camera/gstCamera.cpp du projet jetson-inference
> ```
> $ cd ~/projects/dusty-nv/jetson-inference
> $ vim ./utils/camera/gstCamera.cpp
> ```
> * faire une recherche pour "flip-method = 2" ou flipMethod = "2" (ligne 416)
> * modifier la valeur pour 0 ("flip-method = 0" ou "flipMethod = 0")
> 413         #if NV_TENSORRT_MAJOR > 1 && NV_TENSORRT_MAJOR < 5      // if JetPack 3.1-3.3 (different flip-method)
> 414                 const int flipMethod = 0;                                       // Xavier (w/TRT5) camera is mounted inverted
> 415         #else
> 416                 const int flipMethod = 0;
> 417         #endif
> * sauvegarder le fichier
> * recompiler le projet:
> ```
> $ cd jetson-inference/build
> $ make
> $ sudo make install
> $ sudo ldconfig
> ```
> * ré-exécuter le test d'inférence de segmentation sémantique en temps réel avec la caméra
> ```
> $ cd ~/projects/dusty-nv/jetson-inference
> $ cd ./build/aarch64/bin
> $ ./segnet-camera.py --network=fcn-resnet18-mhp
> ```

### Installation d'un 'matériel' vidéo virtuel (loopback)

### loopback
Références: 
- <https://github.com/umlaeute/v4l2loopback>
- <https://unix.stackexchange.com/questions/5452/manipulating-dev-video>
- <https://sourceforge.net/projects/v4l2vd/>
- <https://gist.github.com/strezh/9114204>

#### Prépare et télécharge
```
$ ls -al /dev/video*
crw-rw----+ 1 root video 81, 0 Feb 15 16:58 /dev/video0
$ cd ~/projects
$ mkdir umlaeute
$ cd umlaeute
$ git clone https://github.com/umlaeute/v4l2loopback.git
$ cd v4l2loopback
```

#### Installation
```
$ make 
$ make && sudo make install
$ sudo depmod -a
```
#### Démarrage
```
$ sudo modprobe v4l2loopback buffers=2
$ ls -al /dev/video*
crw-rw----+ 1 root video 81, 0 Feb 15 16:58 /dev/video0
crw-rw----+ 1 root video 81, 3 Feb 16 14:36 /dev/video1
##### 
```
#### Optionnaly set the FPS
```
./projects/v4l2loopback/utils/v4l2loopback-ctl set-fps 60 /dev/video1
```

#### Validation des capacités du loopback /dev/video1
```
$ v4l2-ctl -d 1 --all 
Driver Info (not using libv4l2):
	Driver name   : v4l2 loopback
	Card type     : Dummy video device (0x0000)
	Bus info      : platform:v4l2loopback-000
	Driver version: 4.9.140
	Capabilities  : 0x85208003
		Video Capture
		Video Output
		Video Memory-to-Memory
		Read/Write
		Streaming
		Extended Pix Format
		Device Capabilities
	Device Caps   : 0x85208003
		Video Capture
		Video Output
		Video Memory-to-Memory
		Read/Write
		Streaming
		Extended Pix Format
		Device Capabilities
Priority: 0
Video input : 0 (loopback: ok)
Video output: 0 (loopback in)
Format Video Output:
	Width/Height      : 0/0
	Pixel Format      : 'BGR4'
	Field             : None
	Bytes per Line    : 0
	Size Image        : 0
	Colorspace        : sRGB
	Transfer Function : Default (maps to sRGB)
	YCbCr/HSV Encoding: Default (maps to ITU-R 601)
	Quantization      : Default (maps to Full Range)
	Flags             : 
Streaming Parameters Video Capture:
	Frames per second: 30.000 (30/1)
	Read buffers     : 8
Streaming Parameters Video Output:
	Frames per second: 30.000 (30/1)
	Write buffers    : 8

User Controls

                    keep_format 0x0098f900 (bool)   : default=0 value=0
              sustain_framerate 0x0098f901 (bool)   : default=0 value=0
                        timeout 0x0098f902 (int)    : min=0 max=100000 step=1 default=0 value=0
               timeout_image_io 0x0098f903 (bool)   : default=0 value=0
```

#### Tests

Démarrer en premier la source vidéo de test (producer) vers /dev/video1:
```
$ gst-launch-1.0 videotestsrc ! v4l2sink device=/dev/video1
```
Ensuite démarrer le consommateur de test (consummer) de la vidéo:
```
$ gst-launch-1.0 v4l2src device=/dev/video1 ! xvimagesink
```
Une petite fenêtre va apparaître avec des frames de différentes couleurs et de la neige.

### Pour streamer une vidéo avec gstreamer-1.0 sur le loopback /dev/video1
- Loopback doit être démarré. 
  - TODO: déterminer si buffers aide et est nécessaire. Référence: <https://github.com/umlaeute/v4l2loopback/issues/83>
```
sudo modprobe v4l2loopback buffers=2
```
- le chemin complet de la vidéo est requis
- tee multiplexer est requis
  - pour forcer une copie de la mémoire dans le pipeline de gstreamer
    - Références: 
      - <https://github.com/umlaeute/v4l2loopback/issues/116>
      - <https://github.com/umlaeute/v4l2loopback/issues/83>
- si width, heigth and framerate sont précisés une erreur est retournée sauf si videoscale et videorate le sont aussi
- ajouter le mode verbeux -v pour voir les détails

Producer du streaming video:

Ctrl+C dans le terminal pour arrêter et fermer la fenêtre de la vidéo. 
```
gst-launch-1.0 -v filesrc location=/home/lefv2603/Downloads/1080p.mp4 ! tee name=qtdemux ! decodebin ! videoconvert ! video/x-raw ! v4l2sink device=/dev/video1

gst-launch-1.0 -v filesrc location=/home/lefv2603/Downloads/1080p.mp4 ! tee ! qtdemux ! decodebin ! videoconvert ! "video/x-raw,format=(string)I420,width=(int)1920,heigth=(int)1080" ! v4l2sink device=/dev/video1

gst-launch-1.0 -v filesrc location=/home/lefv2603/Downloads/1080p.mp4 ! tee ! qtdemux ! decodebin ! videoconvert ! "video/x-raw" ! v4l2sink device=/dev/video1
```

Consummer de test: 

Ctrl+C dans le terminal pour arrêter, ou fermer la fenêtre de la vidéo. 
```
$ gst-launch-1.0 v4l2src device=/dev/video1 ! xvimagesink
```

> **_NOTE Importante:_**
> Le consummer de test ne peut jouer la video si "format=(string)RGB,width=(int)640,heigth=(int)480" est indiqué par le producer. L'erreur "streaming stopped, reason not-negotiated (-4)" est retouné par le consummer. Par contre, ce même producer sera accepté par l'inférence segnet de NVidia. Pas investigué la raison. Le consummer de l'inférence est `gst-launch-1.0 v4l2src device=/dev/video1 ! appsink name=mysink` (après simplification de ma part dans le code cpp. Voir note dans cette page).
>  
> `gst-launch-1.0 -v filesrc location=/home/lefv2603/Downloads/1080p.mp4 ! tee ! qtdemux ! decodebin ! videoconvert ! videoscale ! "video/x-raw,format=(string)RGB,width=(int)640,heigth=(int)480" ! v4l2sink device=/dev/video1`
> ```
> $ gst-launch-1.0 v4l2src device=/dev/video1 ! xvimagesink  
> Setting pipeline to PAUSED ...  
> Pipeline is live and does not need PREROLL ...  
> ERROR: from element /GstPipeline:pipeline0/GstV4l2Src:v4l2src0: Internal data stream error.  
> Additional debug info:  
> gstbasesrc.c(3055): gst_base_src_loop (): /GstPipeline:pipeline0/GstV4l2Src:v4l2src0:  
> **streaming stopped, reason not-negotiated (-4)**  
> ERROR: pipeline doesn't want to preroll.  
> Setting pipeline to PAUSED ...  
> Setting pipeline to READY ...  
> Setting pipeline to NULL ...  
> Freeing pipeline ...  ```

## Test d'inférence segmentation sémantique d'une vidéo

### DVSNet
J'ai essayé de faire l'installation mais j'ai échoué avec l'intallation de PyTorch pour Python 2.7. Il y a besoin de mettre plus d'efforts. Mais cela semble bien intéressant.

Référence: <https://github.com/XUSean0118/DVSNet.git>

### NVidia avec loopback
---
> **_NOTE Importante:_**
> NVidia procure du code pour l'inférence avec une caméra. Afin de faire fonctionner l'inférence avec une vidéo au lieu de la caméra, il est important de faire quelques ajustements dans le code cpp. Voici les détails: 
> - fichier ./dusty-nv/jetson-inference/utils/camera/gstCamera.cpp: 
>   - mettre en commentaire les lignes 429, 432, 434:
> ```
> 428                 ss << "v4l2src device=" << mCameraStr << " ! ";
> 429                 //ss << "video/x-raw, width=(int)" << mWidth << ", height=(int)" << mHeight << ", "; 
> 430 
> 431         #if NV_TENSORRT_MAJOR >= 5
> 432                 //ss << "format=YUY2 ! videoconvert ! video/x-raw, format=RGB ! videoconvert !";
> 433         #else
> 434                 //ss << "format=RGB ! videoconvert ! video/x-raw, format=RGB ! videoconvert !";
> 435         #endif
> 436                 ss << "appsink name=mysink";
> 437 
> 438                 mSource = GST_SOURCE_V4L2;
> ```
> L'objectif est d'avoir "v4l2src device=/dev/video1 ! appsink name=mysink"
> 
> - fichier dusty-nv/jetson-inference/utils/display/glDisplay.cpp:
>   - ajouter les lignes 157 et 158:
> ```
> 153         // retrieve screen info
> 154         const int screenIdx   = DefaultScreen(mDisplayX);
> 155         int screenWidth = DisplayWidth(mDisplayX, screenIdx);
> 156         int screenHeight = DisplayHeight(mDisplayX, screenIdx);
> 157         screenWidth = 1280;
> 158         screenHeight = 720;
> ```
> L'objectif est de démarrer avec la fenêtre (qui va afficher la vidéo) qui ne prend pas tout l'écran. C'est spécifique à mon environnement. 
>
> Il faut recompiler. 
> ```
> $ cd ./dusty-nv/jetson-inference/build
> $ make
> $ sudo make install
> $ sudo ldconfig
> ```
---

Pour faire l'inférence de la vidéo: 
- Dans un premier terminal démarrer le streaming de la vidéo en premier (le producer):
  - il est important de spécifier le chemin complet de la vidéo
  - il est optionnel de spécifier le format, le width et le height

`gst-launch-1.0 -v filesrc location=/home/lefv2603/Downloads/1080p.mp4 ! tee ! qtdemux ! decodebin ! videoconvert ! videoscale ! "video/x-raw,format=(string)RGB,width=(int)640,heigth=(int)480" ! v4l2sink device=/dev/video1`

- Juste après avoir démarrer le producer, **dans un deuxième terminal**, démarrer l'inférence. Une nouvelle fenêtre va apparaître avec la vidéo en train de se faire inférer. 
```
$ cd ~/projects/dusty-nv/jetson-inference
$ cd ./build/aarch64/bin
$ Pour tester avec deepscene
$ ./segnet-camera --camera=/dev/video1 --network=fcn-resnet18-deepscene --visualize=mask --alpha=255 
$ Pour tester avec cityscapes
$ ./segnet-camera --camera=/dev/video1 --network=fcn-resnet18-cityscapes --visualize=mask --alpha=255 
```

> **_NOTE Importante:_**
> Si la vidéo termine, l'inférence et la fenêtre de la vidéo restent bloqués (gelés). Pour les arrêter, utiliser la commande `kill -9`. 
> ```
> $ ps -ef | grep 'scene' (ou ps -ef | grep 'city')
> lefv2603 31864 13527  0 17:50 pts/1    00:00:00 /bin/bash ./inference_resnet18-deepscene_loopback.sh
> lefv2603 31865 31864  9 17:50 pts/1    00:00:05 /usr/bin/python /home/lefv2603/projects/dusty-nv/jetson-inference/build/aarch64/bin/segnet-camera.py --network=fcn-resnet18-deepscene --camera=/dev/video1 --alpha=255
> lefv2603 31898  9597  0 17:51 pts/0    00:00:00 grep --color=auto scene
> $ kill -9 31864 31865
> ```

### en progres... jouer une vidéo prise avec mobile Samsung S8 60FPS
```
gst-launch-1.0 -v filesrc location=/home/lefv2603/projects/gae724/videos/20200308/20200308_150708.mp4 ! 'video/x-raw(memory:NVMM), format=(string)NV12, width=(int)1080, height=(int)1920, framerate=(fraction)60/1' ! nvvidconv ! 'video/x-raw(memory:NVMM), format=(string)NV12, width=(int)1080, height=(int)1920, framerate=(fraction)60/1' ! nvv4l2h264enc ! h264parse ! v4l2sink device=/dev/video1

gst-launch-1.0 -v filesrc location=/home/lefv2603/projects/gae724/videos/20200308/20200308_150708.mp4 ! tee ! queue ! qtdemux ! h264parse ! nvv4l2decoder ! nvvidconv ! 'video/x-raw(memory:NVMM), format=(string)NV12, width=(int)1080, height=(int)1920, framerate=(fraction)60/1' ! nvv4l2h264enc ! h264parse ! v4l2sink device=/dev/video1

gst-launch-1.0 -v filesrc location=/home/lefv2603/projects/gae724/videos/20200308/20200308_150708.mp4 ! tee ! queue ! qtdemux ! h264parse ! nvv4l2decoder ! nvvidconv flip-method=3 ! 'video/x-raw(memory:NVMM), format=(string)NV12, width=(int)1080, height=(int)1920, framerate=(fraction)60/1' ! nvv4l2h264enc ! h264parse ! tee ! queue ! identity drop-allocation=1 ! v4l2sink device=/dev/video1

# Change the rate to 20 FPS and scale
gst-launch-1.0 -v filesrc location=/home/lefv2603/projects/gae724/videos/20200308/20200308_150708.mp4 ! tee ! queue ! qtdemux ! h264parse ! nvv4l2decoder ! nvvidconv flip-method=3 ! videorate ! videoscale ! 'video/x-raw(memory:NVMM), format=(string)I420, width=(int)1080, height=(int)1920, framerate=(fraction)20/1' ! nvv4l2h264enc ! h264parse ! tee ! queue ! identity drop-allocation=1 ! v4l2sink device=/dev/video1

# feed video1 with a rate 60FPS rescaled and flipped with constant bit rates control
gst-launch-1.0 -v filesrc location=/home/lefv2603/projects/gae724/videos/20200308/20200308_150708.mp4 ! tee ! queue ! qtdemux ! h264parse ! nvv4l2decoder ! nvvidconv flip-method=3 ! videorate ! videoscale ! 'video/x-raw(memory:NVMM), format=(string)I420, width=(int)640, height=(int)480, framerate=(fraction)30/1' ! nvv4l2h264enc control-rate=1 bitrate=5000 ! h264parse ! tee ! queue ! identity drop-allocation=1 ! v4l2sink device=/dev/video1

# play the video, with a flip, rescale and rate FPS
gst-launch-1.0 -v filesrc location=/home/lefv2603/projects/gae724/videos/20200308/20200308_150708.mp4 ! tee ! queue ! qtdemux ! h264parse ! nvv4l2decoder ! nvvidconv flip-method=3 ! videorate ! videoscale ! 'video/x-raw(memory:NVMM), format=(string)I420, width=(int)480, height=(int)640, framerate=(fraction)20/1' ! decodebin ! autovideosink -e

# Moving 60FPS video 1080x1920 (portrait)
gst-launch-1.0 -v filesrc location=/home/lefv2603/projects/gae724/videos/20200308/20200308_150945.mp4 ! tee ! queue ! qtdemux ! h264parse ! nvv4l2decoder ! nvvidconv flip-method=3 ! videorate ! videoscale ! 'video/x-raw(memory:NVMM), format=(string)I420, width=(int)480, height=(int)640, framerate=(fraction)60/1' ! decodebin ! autovideosink -e

# Error but tentative to convert to RGB, to folow the model which is working
gst-launch-1.0 -v filesrc location=/home/lefv2603/projects/gae724/videos/20200308/20200308_150708.mp4 ! tee ! queue ! qtdemux ! h264parse ! nvv4l2decoder ! nvvidconv flip-method=3 ! videorate ! videoscale ! 'video/x-raw(memory:NVMM), format=(string)I420, width=(int)640, height=(int)480, framerate=(fraction)60/1' ! nvv4l2h264enc ! h264parse ! decodebin ! videoconvert ! "video/x-raw,format=(string)RGB,width=(int)480,height=(int)640,framerate=(fraction)30/1" ! videoconvert ! tee ! queue ! identity drop-allocation=1 ! v4l2sink device=/dev/video1

# trying things .. not working, still error. 
gst-launch-1.0 --gst-debug -v filesrc location=/home/lefv2603/projects/gae724/videos/20200308/20200308_150708.mp4 ! "video/x-raw,format=(string)UYVY,width=(int)480,height=(int)640,framerate=(fraction)60/1,interlace-mode=(string)progressive,colorimetry=(string)2:4:7:1" ! decodebin ! videoconvert ! tee ! v4l2sink device=/dev/video1 sync=0
```

### GStreamer en mode DEBUG 
```
GST_DEBUG=2 gst-launch-1.0 --gst-debug -v filesrc location=/home/lefv2603/projects/gae724/videos/20200308/20200308_150708.mp4 ! tee ! queue ! qtdemux ! h264parse ! nvv4l2decoder ! nvvidconv flip-method=3 ! videorate ! videoscale ! 'video/x-raw(memory:NVMM), format=(string)I420, width=(int)480, height=(int)640, framerate=(fraction)20/1' ! queue ! tee ! nvv4l2h264enc bitrate=8000000 ! h264parse ! v4l2sink device=/dev/video1 -e
...
0:00:17.073011683 15272   0x55c0555540 WARN          v4l2bufferpool gstv4l2bufferpool.c:1482:gst_v4l2_buffer_pool_dqbuf:<nvv4l2decoder0:pool:sink> v4l2 provided buffer that is too big for the memory it was writing into.  v4l2 claims 1008 bytes used but memory is only 0B.  This is probably a driver bug.
```

### Saisir les logs de gstreamer
```
lefv2603@lefv2603-jetsonnano:~$ GST_DEBUG=3 gst-launch-1.0 --gst-debug -v filesrc location=/home/lefv2603/projects/gae724/videos/20200308/20200308_150708.mp4 ! tee ! queue ! qtdemux ! queue ! h264parse ! nvv4l2decoder ! nvvidconv flip-method=3 ! videorate ! videoscale ! 'video/x-raw(memory:NVMM),format=(string)NV12,width=(int)480,height=(int)640,framerate=(fraction)20/1' ! nvv4l2h264enc ! h264parse ! v4l2sink device=/dev/video1 -e 2>&1 | tee stream.txt
```

## Review of the Jetson nano (benchmark)
Reference: <https://syonyk.blogspot.com/2019/04/benchmarking-nvidia-jetson-nano.html>

## Tips and tricks to train network
<https://towardsdatascience.com/a-bunch-of-tips-and-tricks-for-training-deep-neural-networks-3ca24c31ddc8>
