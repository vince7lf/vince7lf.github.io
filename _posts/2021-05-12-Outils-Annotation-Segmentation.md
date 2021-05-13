Voici une liste des outils qui permettent de faire des annotations pour la segmentation sémantique (mask).  

# Synthèse 
<https://github.com/heartexlabs/label-studio>
<http://aidedemo.westeurope.cloudapp.azure.com:8080/landcover/interface?imgs=4f0ce1d5-ca41-42c2-9d52-8807a560e4c4>


# AIDE Demo Semantic segmentation on the Chesapeake Land Cover satellite imagery
<http://aidedemo.westeurope.cloudapp.azure.com:8080/landcover/interface?imgs=4f0ce1d5-ca41-42c2-9d52-8807a560e4c4>


# 6 outils d'annotation qui sont référencés dans le document AIDE: Accelerating image‐based ecological surveys with interactive machine learning

> Benjamin Kellenberger  
Devis Tuia  
Dan Morris   
(DOI: 10.1111/2041-210X.13489)  
 

> "some interfaces were designed with explicit focus on annotation, like __VATIC__ (Vondrick et al., 2013), __LabelImg__, __VGG Image Annotator__ (Dutta & Zisserman, 2019), __VIOLA__ (Bondi et al., 2017), __LabelMe__ (Russell et al., 2008) and commercial tools like __LabelBox__. A few of them have some form of simple annotation assistance; for example, both VATIC and VIOLA offer interpolation for video data to reduce the number of annotations required. However, more elaborate labelling assistance is often absent."

# The best image annotation platforms for computer vision (+ an honest review of each)
<https://hackernoon.com/the-best-image-annotation-platforms-for-computer-vision-an-honest-review-of-each-dac7f565fea>

## LabelIMG
## VGG Image Annotator
## Supervise.ly
## Labelbox $

# Rémi Tavon (UdeS); eclue; script only ; pas de GUI

Voici celui que j'ai utilisé et qui fonctionne assez bien, mais qui demande une certaine connaissance de Python:

<https://github.com/mcordts/cityscapesScripts/blob/master/cityscapesscripts/annotation/cityscapesLabelTool.py>

Pour votre information: l'annotation se fait par création de polygones et crée un fichier .json, qui est ensuite rasterisé par d'autres outils sur <https://github.com/mcordts/cityscapesScripts/tree/master/cityscapesscripts/preparation>

<https://github.com/mcordts/cityscapesScripts/blob/master/cityscapesscripts/annotation/cityscapesLabelTool.py>

# GitHub (Mickaël Germain  UdeS)

* <https://github.com/Slava/label-tool>

* <https://github.com/Hitachi-Automotive-And-Industry-Lab/semantic-segmentation-editor>

* <https://github.com/heartexlabs/label-studio>

# Outil du CRIM / Justine Boulent

> "j’utilise un code qui a été fait par le crim et que j’ai un peu adapté, qui génère une interface graphique pour faire des annotations de type segmentation. Il est assez pratique pour corriger les annotations (clic gauche tu dessines, clic droit tu effaces). Par contre, il n’y a qu’un outil pinceau, donc ça ne serait peut être pas adapté à toutes les thématiques. Si jamais tu ne trouves pas quelque chose qui te convient en ligne, je pourrais sûrement te le partager."
