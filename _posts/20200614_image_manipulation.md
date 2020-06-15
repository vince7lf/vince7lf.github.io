Manipulation d'images

Pour pouvoir tester le metric ioU ou le f1 score, l'image générée par le modèle doit correspondre aux classes et aux couleurs. Deux options sont alors importantes: 
`--visualize=mask`: permet de ne sauvegarder que la segmentation prédite. L'autre optoin disponible est `overlay` qui affiche la segmentation prédite au dessus de l'image originale. 
`--filter-mode=point`: permet de sauvegarder uniquement la segmentation prédite avec la couleur de chaque pixel correspondant à celle de la classe. L'autre option est `linear`, et "adoucie" la transition entre classe, mais ne permet pas de savoir précisément dans quelle classe le pixel appartient vraiment, et donc si le modèle a bien fait la segmentation. 

```
/home/lefv2603/projects/jetson-inference/build/aarch64/bin/segnet-console --visualize=mask --filter-mode=point --network=fcn-resnet18-deepscene /home/lefv2603/Downloads/freiburg_forest_annotated/test/rgb/b1-09517_Clipped.jpg b1-09517_Clipped_pred.jpg
```

Le résultat est un peu décevant car l'image génére est très grossièrement "pixelisée". 

Il faut de plus arrganger les pixels car ils sont un tout petit peu décalés par rapport aux couleurs de chaque classe. 


- image manipulation with numpy :  <http://www.degeneratestate.org/posts/2016/Oct/23/image-processing-with-numpy/>
