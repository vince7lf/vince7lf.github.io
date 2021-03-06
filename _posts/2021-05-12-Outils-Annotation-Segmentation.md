Voici une liste des outils qui permettent de faire des annotations pour la segmentation sémantique (mask).

# Sélection

## Darwin 
* <https://www.v7labs.com/darwin>

Video:
* <https://www.youtube.com/watch?v=SvihDSAY4TQ&ab_channel=V7>

### Superannotate OpenCV
* <https://superannotate.com/>

Video: 
* <https://www.youtube.com/watch?v=DMS4AljE3ms>

## Supervise.ly
* <https://supervise.ly/>
* <https://deepsystems.ai/products/supervisely>

Video: 
* <https://youtu.be/d53ksO3rFX4>

Commentaires:
* Version communautaire disponible pour les scientifiques et chercheurs
* Sinon version payante. 

## LabelBox
* <https://labelbox.com/>

Videos:
* <https://www.youtube.com/watch?v=j3SP8JSAOwI&ab_channel=LabelboxLabelbox>
* <https://www.youtube.com/watch?v=KPiHk_QmT7g&ab_channel=LabelboxAcademy>

## CVAT (Computer Vision Annotation Tool) Intel
* <https://github.com/openvinotoolkit/cvat>

Video:
* <https://www.youtube.com/watch?v=9Fe_GzMLo3E&ab_channel=NikitaManovichNikitaManovich>

## Hasty.ai
* <https://hasty.ai/>

Video:
* <https://www.youtube.com/watch?v=gkA7qFbE6M4&ab_channel=VittorioMachiavelliVittorioMachiavelli>

## AIDE Annotation Interface for Data-driven Ecology
Demo Semantic segmentation on the Chesapeake Land Cover satellite imagery       
* <https://github.com/microsoft/aerial_wildlife_detection>

Démo:
* <http://aidedemo.westeurope.cloudapp.azure.com:8080/landcover/interface?imgs=4f0ce1d5-ca41-42c2-9d52-8807a560e4c4>

* C'est le 1er choix retenu pour l'instant. Déjà évalué rapidement par Mickaël. 

## VGG Image Annotator
* https://www.robots.ox.ac.uk/~vgg/software/via/
* <https://github.com/michhar/maskrcnn-custom>

Video: 
* <https://www.youtube.com/watch?v=-3WVSxNLk_k>

## VoTT (Visual Object Tagging Tool) Microsoft
* <https://github.com/microsoft/VoTT>

Video: 
* <https://www.youtube.com/watch?v=uDWgWJ5Gpwc>
* <https://www.youtube.com/watch?v=uXxE4Sas3uQ>

## LabelMe
* <https://github.com/CSAILVision/LabelMeAnnotationTool>

# Références: 

## 6 outils d'annotation qui sont référencés dans le document AIDE: Accelerating image‐based ecological surveys with interactive machine learning

> Benjamin Kellenberger  
Devis Tuia  
Dan Morris   
(DOI: 10.1111/2041-210X.13489)  
 

> "some interfaces were designed with explicit focus on annotation, like __VATIC__ (Vondrick et al., 2013), __LabelImg__, __VGG Image Annotator__ (Dutta & Zisserman, 2019), __VIOLA__ (Bondi et al., 2017), __LabelMe__ (Russell et al., 2008) and commercial tools like __LabelBox__. A few of them have some form of simple annotation assistance; for example, both VATIC and VIOLA offer interpolation for video data to reduce the number of annotations required. However, more elaborate labelling assistance is often absent."

## The best image annotation platforms for computer vision (+ an honest review of each)
<https://hackernoon.com/the-best-image-annotation-platforms-for-computer-vision-an-honest-review-of-each-dac7f565fea>

* LabelIMG
* VGG Image Annotator
* Supervise.ly
* Labelbox $

## Rémi T. (UdeS) 

> "Voici celui que j'ai utilisé et qui fonctionne assez bien, mais qui demande une certaine connaissance de Python:
<https://github.com/mcordts/cityscapesScripts/blob/master/cityscapesscripts/annotation/cityscapesLabelTool.py>"

> "Pour votre information: l'annotation se fait par création de polygones et crée un fichier .json, qui est ensuite rasterisé par d'autres outils sur <https://github.com/mcordts/cityscapesScripts/tree/master/cityscapesscripts/preparation>"

* exclu; script seulment ; pas de GUI, pas Web

> "Autrement, il y un autre outil que j'aurais voulu essayer pour l'annotation et qui est bien connu des géomaticiens: QGIS. Des images peuvent y être lues même si elles ne sont pas géoréférencées et l'outil de numérisation (i.e. création de polygones) est assez simple et efficace. Il resterait seulement à coder un plugin pour passer rapidement d'une image à l'autre et sauvegarder automatiquement un fichier du type json ou gpkg (pour les annotations). Je vous laisse cogiter là-dessus."

## Mickaël G. UdeS GitHub

### label-tool GitHub 
* <https://github.com/Slava/label-tool>
 
 ### Hitachi Automotive And Industry Lab semantic segmentation editor GitHub
* <https://github.com/Hitachi-Automotive-And-Industry-Lab/semantic-segmentation-editor>

### Label Studio GitHub
* <https://github.com/heartexlabs/label-studio>

Video:
* <https://www.youtube.com/watch?v=2i6q6uqoC3w&ab_channel=AlexWitsil>

## Recherche Web 

### Sources:
* <https://humansintheloop.org/the-best-image-labeling-tools-for-computer-vision-of-2020/>
* <https://sourceforge.net/software/compare/Prodigy-Annotation-vs-Supervisely/>

* Prodigy
* SuperAnnotate
* Hasty.ai
* TrainingData.io
* Alegion
*	Dataloop AI
*	BasicAI
* Swivl Education Bot, Inc
* Hive Data

### Source: 
* <https://bohemian.ai/blog/image-annotation-tools-which-one-pick-2020/>

* CVAT (Computer Vision Annotation Tool)
* VoTT (Visual Object Tagging Tool)
* dataturks
* Playment

### Source:
* <https://www.sicara.ai/blog/2019-09-01-top-five-open-source-annotation-tools-computer-vision>

* CVAT (Computer Vision Annotation Tool)
  * <https://www.youtube.com/watch?v=6IJ0QN7PBKo&ab_channel=NikitaManovich>
  * <https://www.youtube.com/watch?v=Fh8oKuSUIPs&ab_channel=NikitaManovichNikitaManovich>
* VoTT (Visual Object Tagging Tool)
* dataturks
* MakeSense.AI
* VGG VIA
* LabelMe
* Coco-Annotator

### Source: 
* <https://medium.com/anolytics/how-to-label-data-for-semantic-segmentation-deep-learning-models-907a996f95f7>

* anolytics 
  * <https://www.anolytics.ai/semantic-segmentation-services/>

### source:
* <https://towardsdatascience.com/image-data-labelling-and-annotation-everything-you-need-to-know-86ede6c684b1>

* MakeSense.AI
* Scalable
* RectLabel

### Source: 
* <https://www.researchgate.net/post/How_do_I_create_a_ground_truth_image_for_segmentation_in_digital_image_processing>

* rectlabel.com (Apple)
* Image Labeler app in MATLAB
* inkscape software
* diffgram
  * <https://medium.com/diffgram/how-to-validate-your-deep-learning-model-with-the-diffgram-sdk-tutorial-22234a9a35>
  * <https://github.com/diffgram/diffgram/tree/master/sdk>
  * <https://medium.com/diffgram/fast-annotation-net-a-framework-for-active-learning-in-2018-1c75d6b4af92>
* It may be achieved by image binarization (using useful threshold) , or by utilizing one of the well known image edge detection methods (e.g. differential masks, or band-pass filtered methods like Marr and Hilderith ... etc.) . Or segmentation can be carry on by using image classification (e.g. Mean value, parallel piped, scatter scope, etc.). For more details see my site on R.G
  * <https://www.researchgate.net/profile/Saleh-Ali-15>

### Source: 
* <https://abelling.com/comparison-of-different-annotation-tools-for-computer-vision/>

* Supervise.ly
* Hasty.ai
* CVAT
* Make-Sense
* Darwin
* Heartex
* Scalabel
* Segments.ai

### Source:
* <https://bohemian.ai/blog/image-annotation-tools-which-one-pick-2020/>

* CVAT (Computer Vision Annotation Tool)
* VoTT (Visual Object Tagging Tool)
* LabelBox

### Source:
* <https://en.wikipedia.org/wiki/List_of_manual_image_annotation_tools>

* Computer Vision Annotation Tool (CVAT)
* ImageTagger
* LabelMe
* RectLabel
* VGG Image Annotator (VIA)
* VoTT (Visual Object Tagging Tool)

### Source:
* <https://www.reddit.com/r/computervision/comments/5jxhtc/image_segmentation_labelling_tool/>

* Multimapth.net Facebook
  * <https://github.com/facebookresearch/multipathnet>
* Deepmask facebook
  * <https://github.com/facebookresearch/deepmask>
* uea computer vision image labelling tool 
  * <https://bitbucket.org/ueacomputervision/image-labelling-tool/src/master/>
  * <https://github.com/Britefury/django-labeller>

## Outil du CRIM / Justine B.

> "j’utilise un code qui a été fait par le crim et que j’ai un peu adapté, qui génère une interface graphique pour faire des annotations de type segmentation. Il est assez pratique pour corriger les annotations (clic gauche tu dessines, clic droit tu effaces). Par contre, il n’y a qu’un outil pinceau, donc ça ne serait peut être pas adapté à toutes les thématiques. Si jamais tu ne trouves pas quelque chose qui te convient en ligne, je pourrais sûrement te le partager."

## SIG QGIS, ARC GIS Pro, etc

* création de polygones;
* curseur qui colle aux coutours des lignes / polygones
* géoréférencement

# Articles

## 3 Reasons why AI Assisted Labeling will destroy Manual labor market

<https://medium.com/deep-systems/3-reasons-why-ai-assisted-labeling-will-destroy-manual-labor-market-dbacad7bdf13>

* non objecti, car écrit par supervisely
