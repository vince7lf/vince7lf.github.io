# Manipulation d'images

## Prétraitement de l'image générée

Pour pouvoir tester le metric ioU ou le f1 score, l'image générée par le modèle doit correspondre aux classes et aux couleurs. Deux options sont alors importantes: 
`--visualize=mask`: permet de ne sauvegarder que la segmentation prédite. L'autre optoin disponible est `overlay` qui affiche la segmentation prédite au dessus de l'image originale. 
`--filter-mode=point`: permet de sauvegarder uniquement la segmentation prédite avec la couleur de chaque pixel correspondant à celle de la classe. L'autre option est `linear`, et "adoucie" la transition entre classe, mais ne permet pas de savoir précisément dans quelle classe le pixel appartient vraiment, et donc si le modèle a bien fait la segmentation. 

```
/home/lefv2603/projects/jetson-inference/build/aarch64/bin/segnet-console --visualize=mask --filter-mode=point --network=fcn-resnet18-deepscene /home/lefv2603/Downloads/freiburg_forest_annotated/test/rgb/b1-09517_Clipped.jpg b1-09517_Clipped_pred.jpg
```

Le résultat est un peu décevant car l'image générée est très grossièrement "pixelisée". 

Il faut de plus arranger les pixels car ils sont un tout petit peu décalés par rapport aux couleurs de chaque classe. 

Il fat de pleus générer l'image avec le code couleur de DeepScene pour que la correspondance entre classe se fasse plus facilement. 

Références:
- image manipulation with numpy :  <http://www.degeneratestate.org/posts/2016/Oct/23/image-processing-with-numpy/>

## Classes et couleurs du modèle NVidia pour DeepScene
### Classes
```
cat /home/lefv2603/projects/jetson-inference/data/networks/FCN-ResNet18-DeepScene-576x320/classes.txt 
trail
grass
vegetation
obstacle
sky
```

### Couleurs
```
cat /home/lefv2603/projects/jetson-inference/data/networks/FCN-ResNet18-DeepScene-576x320/colors.txt 
200 155 75 180
85 210 100
15 100 20
255 185 0
0 120 255 150
```

### Classes et couleurs du dataset de DeepScene
Référence: /home/lefv2603/Downloads/freiburg_forest_annotated/README.txt
```
Class		R	G	B	ID
Void		- 	- 	-	0
Road            170 	170 	170	1
Grass           0 	255 	0	2
Vegetation      102 	102 	51	3
Tree            0 	60 	0	3
Sky             0 	120 	255	4
Obstacle        0 	0 	0	5
```

### Codes

```
from skimage import io
import numpy as np
import os

home_gt='/home/vincelf/downloads/freiburg_forest_annotated/test/GT_color'
gt_color='b1-09517_Clipped.png'
gt_color_f = os.path.join(home_gt, gt_color)
gt_color_img = io.imread(gt_color_f)
gt_color_img.shape
(481, 868, 3)
set( tuple(v) for m2d in gt_color_img for v in m2d )
{(170, 170, 170), (102, 102, 51), (0, 255, 0), (0, 120, 255)}

home_pred='/home/vincelf/upload'
color_pred='b1-09517_Clipped_pred.jpg'
color_pred_f = os.path.join(home_pred, color_pred)
color_pred_img = io.imread(gt_color_pred_f)
color_pred_img.shape
(481, 868, 3)
set( tuple(v) for m2d in color_pred_img for v in m2d )
{(201, 156, 75), (87, 211, 101), (16, 99, 21), (84, 211, 98), (199, 157, 75), (0, 120, 255), (202, 155, 75), (198, 155, 76), (84, 210, 100), (16, 101, 20), (17, 100, 22), (16, 100, 22), (0, 120, 253), (200, 155, 74), (200, 154, 76), (84, 210, 102), (2, 120, 255), (17, 100, 20), (201, 154, 74), (2, 119, 255), (85, 211, 101), (201, 154, 76), (13, 100, 19), (86, 209, 102), (201, 155, 77), (0, 119, 255), (198, 156, 74), (14, 101, 22), (15, 100, 17), (199, 156, 77), (14, 101, 20), (16, 99, 19), (17, 102, 21), (0, 121, 254), (15, 100, 19), (86, 210, 100), (202, 155, 77), (0, 121, 255), (13, 100, 21), (15, 99, 21), (86, 210, 98), (83, 209, 99), (85, 209, 99), (1, 119, 255)}
```
