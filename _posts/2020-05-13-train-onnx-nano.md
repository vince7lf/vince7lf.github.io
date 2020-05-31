# Ré-entraine les models disponibles pour le Jetson-Nano, et exportation vers ONNX pour l'inférence

## Pré-requis: 
* code non compatible avec Python 3
Le code de pytorch-segmentation-master n'est pas compatible avec Python 3. Il faut exécuter l'entrainement avec Python 2.7.
Il ne faut pas utiliser le module tensorrt car il force Python 3. Utiliser le module tensorrt une fois que l'entrainement est complété. 
```
(env) [vincelf@blg4107 vision-0.3.0-dusty-ng]$ cd ~/pytorch-segmentation-master
(env) [vincelf@blg4107 pytorch-segmentation-master]$ python train.py -a fcn_resnet18 --dataset deepscene --model-dir ./model_output --dist-url 'tcp://127.0.0.1:5556' /home/vincelf/downloads/freiburg_forest_annotated
pytorch-segmentation/datasets/__init__.py
Traceback (most recent call last):
  File "train.py", line 24, in <module>
    from datasets.cityscapes_utils import get_cityscapes
  File "/home/vincelf/pytorch-segmentation-master/datasets/cityscapes_utils.py", line 18
    print self.classes
             ^
SyntaxError: Missing parentheses in call to 'print'. Did you mean print(print self.classes)?
(env) [vincelf@blg4107 pytorch-segmentation-master]$ vi /home/vincelf/pytorch-segmentation-master/datasets/cityscapes_utils.py
```
* valider que l'environnement d'entrainement et de creation de l'ONNX (Compute Canada) est compatible avec l'exécution de l'inférence ONNX sur le Jetson (version de tensorRT + CUDA).

### Jetson nano
Sur le Jetson nano, il est possible de vérifier quelles sont les version de tensorRT et cuda. 

Référence de compatibilite TensorRT: <https://docs.nvidia.com/deeplearning/tensorrt/release-notes/tensorrt-5.html>

#### Pour TensorRT: 

Reference: <https://forums.developer.nvidia.com/t/how-to-check-my-tensorrt-version/56374/9>
```
dpkg -l | grep TensorRT
ou 
dpkg -l | grep nvinfer

lefv2603@lefv2603-jetsonnano:~$ dpkg -l | grep nvinfer
ii  libnvinfer-bin                                6.0.1-1+cuda10.0                                 arm64        TensorRT binaries
ii  libnvinfer-dev                                6.0.1-1+cuda10.0                                 arm64        TensorRT development libraries and headers
ii  libnvinfer-doc                                6.0.1-1+cuda10.0                                 all          TensorRT documentation
ii  libnvinfer-plugin-dev                         6.0.1-1+cuda10.0                                 arm64        TensorRT plugin libraries
ii  libnvinfer-plugin6                            6.0.1-1+cuda10.0                                 arm64        TensorRT plugin libraries
ii  libnvinfer-samples                            6.0.1-1+cuda10.0                                 all          TensorRT samples
ii  libnvinfer6                                   6.0.1-1+cuda10.0                                 arm64        TensorRT runtime libraries
ii  python-libnvinfer                             6.0.1-1+cuda10.0                                 arm64        Python bindings for TensorRT
ii  python-libnvinfer-dev                         6.0.1-1+cuda10.0                                 arm64        Python development package for TensorRT
ii  python3-libnvinfer                            6.0.1-1+cuda10.0                                 arm64        Python 3 bindings for TensorRT
ii  python3-libnvinfer-dev                        6.0.1-1+cuda10.0                                 arm64        Python 3 development package for TensorRT

lefv2603@lefv2603-jetsonnano:~$ dpkg -l | grep TensorRT
ii  graphsurgeon-tf                               6.0.1-1+cuda10.0                                 arm64        GraphSurgeon for TensorRT package
ii  libnvinfer-bin                                6.0.1-1+cuda10.0                                 arm64        TensorRT binaries
ii  libnvinfer-dev                                6.0.1-1+cuda10.0                                 arm64        TensorRT development libraries and headers
ii  libnvinfer-doc                                6.0.1-1+cuda10.0                                 all          TensorRT documentation
ii  libnvinfer-plugin-dev                         6.0.1-1+cuda10.0                                 arm64        TensorRT plugin libraries
ii  libnvinfer-plugin6                            6.0.1-1+cuda10.0                                 arm64        TensorRT plugin libraries
ii  libnvinfer-samples                            6.0.1-1+cuda10.0                                 all          TensorRT samples
ii  libnvinfer6                                   6.0.1-1+cuda10.0                                 arm64        TensorRT runtime libraries
ii  libnvonnxparsers-dev                          6.0.1-1+cuda10.0                                 arm64        TensorRT ONNX libraries
ii  libnvonnxparsers6                             6.0.1-1+cuda10.0                                 arm64        TensorRT ONNX libraries
ii  libnvparsers-dev                              6.0.1-1+cuda10.0                                 arm64        TensorRT parsers libraries
ii  libnvparsers6                                 6.0.1-1+cuda10.0                                 arm64        TensorRT parsers libraries
ii  nvidia-container-csv-tensorrt                 6.0.1.10-1+cuda10.0                              arm64        Jetpack TensorRT CSV file
ii  python-libnvinfer                             6.0.1-1+cuda10.0                                 arm64        Python bindings for TensorRT
ii  python-libnvinfer-dev                         6.0.1-1+cuda10.0                                 arm64        Python development package for TensorRT
ii  python3-libnvinfer                            6.0.1-1+cuda10.0                                 arm64        Python 3 bindings for TensorRT
ii  python3-libnvinfer-dev                        6.0.1-1+cuda10.0                                 arm64        Python 3 development package for TensorRT
ii  tensorrt                                      6.0.1.10-1+cuda10.0                              arm64        Meta package of TensorRT
ii  uff-converter-tf                              6.0.1-1+cuda10.0                                 arm64        UFF converter for TensorRT package
lefv2603@lefv2603-jetsonnano:~$ 

```
#### Pour Cuda (compilateur nvcc)
```
nvcc --version
```
* Au besoin isntaller uine Jeppack approprié. Par contre il faut aller voir dans le Jetpack quel version de TensorRT et Cuda est incluse. 
  * <https://developer.nvidia.com/embedded/jetpack>
  * <https://developer.nvidia.com/embedded/jetpack-archive>
  * <https://developer.nvidia.com/embedded/linux-tegra-r321>
  
* il y a un forum pour AI & DeepLearning chez NVidia
<https://forums.developer.nvidia.com/c/ai-deep-learning/86>

### Compute Canada 
Compute Canada mets à disponition tensorrt/6.0.1.5
```
[vincelf@beluga2 trtexec]$ module spider tensorrt

----------------------------------------------------------------------------
  tensorrt: tensorrt/6.0.1.5
----------------------------------------------------------------------------
    Description:
      NVIDIA TensorRT is a platform for high-performance deep learning
      inference


    You will need to load all module(s) on any one of the lines below before the "tensorrt/6.0.1.5" module is available to load.

      nixpkgs/16.09  cudacore/.10.1.243
 
    Help:
      
      Description
      ===========
      NVIDIA TensorRT is a platform for high-performance deep learning inference
      
      
      More information
      ================
       - Homepage: https://developer.nvidia.com/tensorrt
      
      
      Compatible modules
      ==================
      This module is compatible with the following modules, one of each line is required:
      * python/3.6.3 (default), python/3.7.4
```

Pour charger le module, il faut charger cudacore en pré-requis
```
module load python/3.6 cuda/10.0 cudnn cudacore/.10.1.243 tensorrt
```
Then try trtexec (no need to add it on the PATH)
```
[vincelf@blg4103 ~]$ trtexec
&&&& RUNNING TensorRT.trtexec # trtexec
=== Model Options ===
  --uff=<file>                UFF model
  --onnx=<file>               ONNX model
  --model=<file>              Caffe model (default = no model, random weights used)
  --deploy=<file>             Caffe prototxt file
  --output=<name>[,<name>]*   Output names (it can be specified multiple times); at least one output is required for UFF and Caffe
  --uffInput=<name>,X,Y,Z     Input blob name and its dimensions (X,Y,Z=C,H,W), it can be specified multiple times; at least one is required for UFF models
  --uffNHWC                   Set if inputs are in the NHWC layout instead of NCHW (use X,Y,Z=H,W,C order in --uffInput)
```

### Ajouter trtexec dans le PATH (Jetson Nano)
```
vi ~/.bashrc
export PATH=${PATH}:/usr/local/cuda/bin:/usr/src/tensorrt/bin
:wq
source ~/.bashrc (ou . ~/.bashrc)
```


## tester le ONNX avec trtexec
References: 
* <https://forums.developer.nvidia.com/t/where-is-trtexec/73514>
* <https://github.com/NVIDIA/TensorRT/issues/148>
* <https://forums.developer.nvidia.com/t/tensorrt-cannot-use-precision-int32-with-weights-of-type-float/122490>

Il y a un utilitaire trtexec qui vient avec tensorRT. Par contre il faut le compiler sur le Jetson Nano. 
Pour Compute Canada, il faut loader le module tensorrt (`module load tensorrt`) mais par contre cela load aussi Python 3.6 avec.
```
cd /usr/src/tensorrt/samples/trtexec/
sudo make
```
L'executable est dans /usr/src/tensorrt/bin/trtexec
```
lefv2603@lefv2603-jetsonnano:~/projects/dusty-nv/jetson-inference$ /usr/src/tensorrt/bin/trtexec --onnx=/home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx --explicitBatch
&&&& RUNNING TensorRT.trtexec # /usr/src/tensorrt/bin/trtexec --onnx=/home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx --explicitBatch
[04/14/2020-22:02:03] [I] === Model Options ===
[04/14/2020-22:02:03] [I] Format: ONNX
[04/14/2020-22:02:03] [I] Model: /home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx
[04/14/2020-22:02:03] [I] Output:
[04/14/2020-22:02:03] [I] === Build Options ===
[04/14/2020-22:02:03] [I] Max batch: explicit
[04/14/2020-22:02:03] [I] Workspace: 16 MB
[04/14/2020-22:02:03] [I] minTiming: 1
[04/14/2020-22:02:03] [I] avgTiming: 8
[04/14/2020-22:02:03] [I] Precision: FP32
[04/14/2020-22:02:03] [I] Calibration: 
[04/14/2020-22:02:03] [I] Safe mode: Disabled
[04/14/2020-22:02:03] [I] Save engine: 
[04/14/2020-22:02:03] [I] Load engine: 
[04/14/2020-22:02:03] [I] Inputs format: fp32:CHW
[04/14/2020-22:02:03] [I] Outputs format: fp32:CHW
[04/14/2020-22:02:03] [I] Input build shapes: model
[04/14/2020-22:02:03] [I] === System Options ===
[04/14/2020-22:02:03] [I] Device: 0
[04/14/2020-22:02:03] [I] DLACore: 
[04/14/2020-22:02:03] [I] Plugins:
[04/14/2020-22:02:03] [I] === Inference Options ===
[04/14/2020-22:02:03] [I] Batch: Explicit
[04/14/2020-22:02:03] [I] Iterations: 10 (200 ms warm up)
[04/14/2020-22:02:03] [I] Duration: 10s
[04/14/2020-22:02:03] [I] Sleep time: 0ms
[04/14/2020-22:02:03] [I] Streams: 1
[04/14/2020-22:02:03] [I] Spin-wait: Disabled
[04/14/2020-22:02:03] [I] Multithreading: Enabled
[04/14/2020-22:02:03] [I] CUDA Graph: Disabled
[04/14/2020-22:02:03] [I] Skip inference: Disabled
[04/14/2020-22:02:03] [I] === Reporting Options ===
[04/14/2020-22:02:03] [I] Verbose: Disabled
[04/14/2020-22:02:03] [I] Averages: 10 inferences
[04/14/2020-22:02:03] [I] Percentile: 99
[04/14/2020-22:02:03] [I] Dump output: Disabled
[04/14/2020-22:02:03] [I] Profile: Disabled
[04/14/2020-22:02:03] [I] Export timing to JSON file: 
[04/14/2020-22:02:03] [I] Export profile to JSON file: 
[04/14/2020-22:02:03] [I] 
----------------------------------------------------------------
Input filename:   /home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx
ONNX IR version:  0.0.4
Opset version:    9
Producer name:    pytorch
Producer version: 1.3
Domain:           
Model version:    0
Doc string:       
----------------------------------------------------------------
WARNING: ONNX model has a newer ir_version (0.0.4) than this parser was built against (0.0.3).
WARNING: Your ONNX model has been generated with INT64 weights, while TensorRT does not natively support INT64. Attempting to cast down to INT32.
Successfully casted down to INT32.
While parsing node number 2 [Gather]:
ERROR: onnx2trt_utils.hpp:347 In function convert_axis:
[8] Assertion failed: axis >= 0 && axis < nbDims
[04/14/2020-22:02:18] [E] Failed to parse onnx file
[04/14/2020-22:02:18] [E] Parsing model failed
[04/14/2020-22:02:18] [E] Engine could not be created
&&&& FAILED TensorRT.trtexec # /usr/src/tensorrt/bin/trtexec --onnx=/home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx --explicitBatch
```

## Dataset deepscene freiburg_forest_multispectral_annotated 
* Téléchargement des données annotées de la forêt de Freiburg
```
# À faire dans l'environnement de Compute Canada:
# move to the home directory
cd ~

# retrieve the deepscene freiburg_forest_multispectral_annotated (1.2Gb); really quick from compute canada (>13Mb/sec)
wget http://deepscene.cs.uni-freiburg.de/static/datasets/freiburg_forest_multispectral_annotated.tar.gz

# untar-zip the deepscene freiburg_forest_multispectral_annotated (1.2Gb) directly inside the home folder
tar xvf freiburg_forest_multispectral_annotated.tar.gz 
```

## Préparation de l'environnement d'entrainement (Compute Canada)
### session interactive 
```
# start the interactive resource alllocation; time consuming; have to wait for resource available; can take 5-10-15 minutes
salloc --account=def-germ2201-ab --gres=gpu:1 --cpus-per-task=10 --mem=48000M --time=0-3:00
```
### Environnement virtuel Python
* Python 2.7 cuda 10.0 tensorrt/6.0.1.5
* Création de l'environnement Python virtuel
* Installation des modules Python requis
```
# load modules python 2.7 and cuda 10.1. torchvision requires 10.1. 
module load python/2.7 cuda/10.1 cudnn
# module load python/2.7 cuda/10.0 cudnn # will failt because torchvision requires cuda 10.1. 
# with tensorrt mais attention car il force Python 3, et l'entrainement de pytorch-segmentation-master échoue (pas compatible Python 3)
# module load python/2.7 cuda/10.0 cudnn cudacore/.10.1.243 tensorrt 

# clean the python virtual env
rm -rf $SLURM_TMPDIR/env

# create the virtual env
virtualenv --no-download $SLURM_TMPDIR/env

# activate the python virtual env
source $SLURM_TMPDIR/env/bin/activate

# check the python and pip version (2.7)
python --version
pip --version
nvcc --version # check CUDA compiler version

# install requirements
pip install --no-index torch==1.3.0 # 1.3.0 #1.1.0 error torch compiled with CUDA==10.0 and torchvision compiled with CUDA==10.1
pip install --no-index scikit-learn
pip install --no-index six
pip install --no-index pillow==6.1.0 # version 7.0.0 break torchvision
pip install --no-index ~/Cython-0.29.17.tar.gz 
pip install --no-index ~/pycocotools-2.0.0.tar.gz 
```

## Récupération de la version modifiée de torchvision de NVidia, et qui contient des patches pour l'exportation vers ONNX
```
# retrieve the fork for vision-0.3.0 from dusty-nv
cd ~
git clone https://github.com/dusty-nv/vision.git

# retrieve the code for training of semantic segmentation networks with PyTorch for jetson nano
cd ~
git clone https://github.com/dusty-nv/pytorch-segmentation.git

# build torchvision; version from nvidia dusty-ng githu repo; cela prend environs 5 minutes
# TODO command line to retrieve the vision 0.3.0 code; linked provided by dusty-ng nvidia
# It's also meant to be used against this fork of torchvision, which includes 
# some patches for exporting to ONNX and adds fcn_resnet18 and fcn_resnet34:
#
#           https://github.com/dusty-nv/vision/tree/v0.3.0
```
## Compilation de torchvision avec la même version du compilateur que pytorch
> __NOTE IMPORTANTE__: il est important d'avoir la même version de CUDA entre Pytorch et TochVision. La commande `nvcc --version` donne la version du compilateur CUDA qui sera utilisé pour la compilation de torchvision, et qui doit être la même que celle qui a été utilisée par PyTorch. 

```
cd ~/vision-0.3.0-dusty-ng
rm -rf build/
python setup.py build install

# run some torchvision test
# if it fails here... not good. bloqued. 
python test/test_models.py 
```

## Prépare les images de la forêt de Freiburg pour le modèle 
```
# re-map the photos if not alread done; time consuming; around 230 pictures to rempa, 230 seconds; arounfd 5 minutes; x2 also to do for the test photos
# TODO command line to retrieve the code
cd ~/pytorch-segmentation-master
python deepscene_remap.py ~/downloads/freiburg_forest_annotated/train/GT_color ~/downloads/freiburg_forest_annotated/train/GT_index
python deepscene_remap.py ~/downloads/freiburg_forest_annotated/train/GT_color ~/downloads/freiburg_forest_annotated/train/GT_index
python deepscene_remap.py ~/downloads/freiburg_forest_annotated/test/GT_color ~/downloads/freiburg_forest_annotated/test/GT_index
```

## Démarre l'entrainement

### Entrainement pour segnet101
```
# start the training
python train.py -a fcn_resnet101 --dataset deepscene --model-dir ./model_output --dist-url 'tcp://127.0.0.1:5556' /home/vincelf/downloads/freiburg_forest_annotated
```
### Entrainement pour segnet18
#### Préparation 
* Il faut télécharger en avance le model déjà entrainé et le copier sur le serveur de Compute Canada, car depuis le serveur de Compute Canada le téléchargement est impossible

```
# to train with segnet18, segnet18 requires resnet18-5c106cde.pth
# download from jetson nano
lefv2603@lefv2603-jetsonnano:~/projects/dusty-nv/jetson-inference$ wget https://download.pytorch.org/models/resnet18-5c106cde.pth

# copy to beluga
lefv2603@lefv2603-jetsonnano:~/projects/dusty-nv/jetson-inference$scp /home/lefv2603/projects/dusty-nv/resnet18-5c106cde.pth vincelf@beluga.computecanada.ca:~/upload

# on beluga copy to the torch cache checkpoints folder
(env) [vincelf@blg4103 pytorch-segmentation-master]$ mv ~/upload/resnet18-5c106cde.pth /home/vincelf/.cache/torch/checkpoints

# 
(env) [vincelf@blg4103 pytorch-segmentation-master]$ ls -al cp /home/vincelf/.cache/torch/checkpoints
ls: cannot access 'cp': No such file or directory
/home/vincelf/.cache/torch/checkpoints:
total 672138
drwxr-x--- 2 vincelf vincelf     25600 May  9 16:44 .
drwxr-x--- 3 vincelf vincelf     25600 Apr 19 20:39 ..
-rw-r----- 1 vincelf vincelf 244545539 Apr 19 21:43 deeplabv3_resnet101_coco-586e9e4e.pth
-rw-r----- 1 vincelf vincelf 217800805 Apr 20 22:08 fcn_resnet101_coco-7ecb50ca.pth
-rw-r----- 1 vincelf vincelf 178728960 Apr 19 21:27 resnet101-5d3b4d8f.pth
-rw-r----- 1 vincelf vincelf  46827520 May  9 16:23 resnet18-5c106cde.pth
```

#### Démarre l'entrainement avec segnet18
```
# start the resnet18 training
cd ~/pytorch-segmentation-master
python train.py -a fcn_resnet18 --dataset deepscene --model-dir ./model_output --dist-url 'tcp://127.0.0.1:5556' /home/vincelf/downloads/freiburg_forest_annotated
```

## Exporte le model vers le format ONNX
```
# export to onnx model
python onnx_export.py --input model_output/model_best.pth --output test.onnx
python onnx_export.py --input model_output/model_best.pth --output resnet18-vlf.onnx
```

## Test le modèle avec l'inférence ONNX
```
# From the jetson nano: copy the onnx model
scp vincelf@beluga.computecanada.ca:/home/vincelf/pytorch-segmentation-master/test.onnx /home/lefv2603/projects/dusty-nv/pytorch-segmentation-master
lefv2603@lefv2603-jetsonnano:~/projects/dusty-nv/jetson-inference/build/aarch64/bin$ scp vincelf@beluga.computecanada.ca:/home/vincelf/pytorch-segmentation-master/resnet18-vlf.onnx /home/lefv2603/projects/dusty-nv/pytorch-segmentation-master

# From the jetson nano, test the inference
cd ~/projects/dusty-nv/jetson-inference/build/aarch64/bin
lefv2603@lefv2603-jetsonnano:~/projects/dusty-nv/jetson-inference/build/aarch64/bin$ ./segnet-camera --network '/home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx' --labels /home/lefv2603/projects/dusty-nv/jetson-inference/data/networks/FCN-ResNet18-DeepScene-576x320/classes.txt --colors /home/lefv2603/projects/dusty-nv/jetson-inference/data/networks/FCN-ResNet18-DeepScene-576x320/colors.txt --input_blob input_0 --output_blob ouput_0
```

## Désactiver et effacer l'environnement virtuel Python sur le serveur de Compute Canada
```
# deactivate python env

# clean the python virtual env
rm -rf $SLURM_TMPDIR/env
```

# Erreurs
## ERROR: Could not install packages due to an EnvironmentError: [Errno 30] Read-only file system:
```
(env) [vincelf@blg5708 ~]$ pip install --no-index torch==1.3.0
Ignoring pip: markers 'python_version < "3"' don't match your environment
Looking in links: /cvmfs/soft.computecanada.ca/custom/python/wheelhouse/avx512, /cvmfs/soft.computecanada.ca/custom/python/wheelhouse/avx2, /cvmfs/soft.computecanada.ca/custom/python/wheelhouse/generic
Collecting torch==1.3.0
Collecting numpy (from torch==1.3.0)
Installing collected packages: numpy, torch
ERROR: Could not install packages due to an EnvironmentError: [Errno 30] Read-only file system: '/cvmfs/soft.computecanada.ca/easybuild/software/2017/Core/python/3.7.4/lib/python3.7/site-packages/numpy-1.18.1.dist-info'
```
Probablement que la session salloc est terminé.

La façon dont j'ai corrigé est:
- deactivate
- sortir de salloc
- ferme la session ssh
- redémarre une session ssh
- refaire salloc
- recreer le python env
- activate


## ImportError: cannot import name 'PILLOW_VERSION' from 'PIL'
```
New Pillow version (7.0.0) breaks torchvision (ImportError 
```
pip install --no-index pillow>=6.2.1 # version 7 breaks torchvision

## RuntimeError: Detected that PyTorch and torchvision were compiled with different CUDA versions. PyTorch has CUDA Version=10.0 and torchvision has CUDA Version=10.1. Please reinstall the torchvision that matches your PyTorch install.

I tried installing torch 1.1.0 (pip install --no-index torch==1.1.0) but got this error; why I tried ? It's to avoid the following warning when running the onnx, and maybe avoid the assertion  
```
----------------------------------------------------------------
WARNING: ONNX model has a newer ir_version (0.0.4) than this parser was built against (0.0.3).
[TRT]   129:Constant -> 
[TRT]   130:Shape -> (4)
WARNING: Your ONNX model has been generated with INT64 weights, while TensorRT does not natively support INT64. Attempting to cast down to INT32.
Successfully casted down to INT32.
While parsing node number 2 [Gather -> "131"]:
--- Begin node ---
input: "130"
input: "129"
output: "131"
op_type: "Gather"
attribute {
  name: "axis"
  i: 0
  type: INT
}

--- End node ---
ERROR: onnx2trt_utils.hpp:347 In function convert_axis:
[8] Assertion failed: axis >= 0 && axis < nbDims
[TRT]   failed to parse ONNX model '/home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx'
[TRT]   device GPU, failed to load /home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx
segNet -- failed to initialize.
segnet-camera:   failed to initialize imageNet
```
 
# RuntimeError: Only tuples, lists and Variables supported as JIT inputs, but got OrderedDict
With cuda=10.0, pytorch==1.1.0, torchvision 0.3.0 from dusty-nv (recompiled)
```
(env) [vincelf@blg4102 pytorch-segmentation-master]$ python onnx_export.py --input model_output/model_best.pth --output resnet18-vlf.onnx
Namespace(input='model_output/model_best.pth', model_dir='', output='resnet18-vlf.onnx')
running on device cuda:0
loading checkpoint:  model_output/model_best.pth
checkpoint accuracy: 73.291% mean IoU, 92.262% accuracy
using model:  fcn_resnet18
num classes:  5
torchvision.models.segmentation.fcn_resnet18()
torchvision.models.segmentation.FCN() => configuring model for training
FCN(
  (backbone): IntermediateLayerGetter(
    (conv1): Conv2d(3, 64, kernel_size=(7, 7), stride=(2, 2), padding=(3, 3), bias=False)
    (bn1): BatchNorm2d(64, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
    (relu): ReLU(inplace)
    (maxpool): MaxPool2d(kernel_size=3, stride=2, padding=1, dilation=1, ceil_mode=False)
    (layer1): Sequential(
      (0): BasicBlock(
        (conv1): Conv2d(64, 64, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn1): BatchNorm2d(64, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        (relu): ReLU(inplace)
        (conv2): Conv2d(64, 64, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn2): BatchNorm2d(64, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
      )
      (1): BasicBlock(
        (conv1): Conv2d(64, 64, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn1): BatchNorm2d(64, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        (relu): ReLU(inplace)
        (conv2): Conv2d(64, 64, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn2): BatchNorm2d(64, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
      )
    )
    (layer2): Sequential(
      (0): BasicBlock(
        (conv1): Conv2d(64, 128, kernel_size=(3, 3), stride=(2, 2), padding=(1, 1), bias=False)
        (bn1): BatchNorm2d(128, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        (relu): ReLU(inplace)
        (conv2): Conv2d(128, 128, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn2): BatchNorm2d(128, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        (downsample): Sequential(
          (0): Conv2d(64, 128, kernel_size=(1, 1), stride=(2, 2), bias=False)
          (1): BatchNorm2d(128, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        )
      )
      (1): BasicBlock(
        (conv1): Conv2d(128, 128, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn1): BatchNorm2d(128, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        (relu): ReLU(inplace)
        (conv2): Conv2d(128, 128, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn2): BatchNorm2d(128, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
      )
    )
    (layer3): Sequential(
      (0): BasicBlock(
        (conv1): Conv2d(128, 256, kernel_size=(3, 3), stride=(2, 2), padding=(1, 1), bias=False)
        (bn1): BatchNorm2d(256, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        (relu): ReLU(inplace)
        (conv2): Conv2d(256, 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn2): BatchNorm2d(256, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        (downsample): Sequential(
          (0): Conv2d(128, 256, kernel_size=(1, 1), stride=(2, 2), bias=False)
          (1): BatchNorm2d(256, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        )
      )
      (1): BasicBlock(
        (conv1): Conv2d(256, 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn1): BatchNorm2d(256, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        (relu): ReLU(inplace)
        (conv2): Conv2d(256, 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn2): BatchNorm2d(256, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
      )
    )
    (layer4): Sequential(
      (0): BasicBlock(
        (conv1): Conv2d(256, 512, kernel_size=(3, 3), stride=(2, 2), padding=(1, 1), bias=False)
        (bn1): BatchNorm2d(512, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        (relu): ReLU(inplace)
        (conv2): Conv2d(512, 512, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn2): BatchNorm2d(512, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        (downsample): Sequential(
          (0): Conv2d(256, 512, kernel_size=(1, 1), stride=(2, 2), bias=False)
          (1): BatchNorm2d(512, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        )
      )
      (1): BasicBlock(
        (conv1): Conv2d(512, 512, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn1): BatchNorm2d(512, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
        (relu): ReLU(inplace)
        (conv2): Conv2d(512, 512, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
        (bn2): BatchNorm2d(512, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
      )
    )
  )
  (classifier): FCNHead(
    (0): Conv2d(512, 128, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False)
    (1): BatchNorm2d(128, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
    (2): ReLU()
    (3): Dropout(p=0.1)
    (4): Conv2d(128, 5, kernel_size=(1, 1), stride=(1, 1))
  )
)

input size:  320x320
exporting model to ONNX...
Traceback (most recent call last):
  File "onnx_export.py", line 73, in <module>
    torch.onnx.export(model, input, opt.output, verbose=True, input_names=input_names, output_names=output_names)
  File "/localscratch/vincelf.7646845.0/env/lib/python2.7/site-packages/torch/onnx/__init__.py", line 25, in export
    return utils.export(*args, **kwargs)
  File "/localscratch/vincelf.7646845.0/env/lib/python2.7/site-packages/torch/onnx/utils.py", line 131, in export
    strip_doc_string=strip_doc_string)
  File "/localscratch/vincelf.7646845.0/env/lib/python2.7/site-packages/torch/onnx/utils.py", line 363, in _export
    _retain_param_name, do_constant_folding)
  File "/localscratch/vincelf.7646845.0/env/lib/python2.7/site-packages/torch/onnx/utils.py", line 266, in _model_to_graph
    graph, torch_out = _trace_and_get_graph_from_model(model, args, training)
  File "/localscratch/vincelf.7646845.0/env/lib/python2.7/site-packages/torch/onnx/utils.py", line 225, in _trace_and_get_graph_from_model
    trace, torch_out = torch.jit.get_trace_graph(model, args, _force_outplace=True)
  File "/localscratch/vincelf.7646845.0/env/lib/python2.7/site-packages/torch/jit/__init__.py", line 231, in get_trace_graph
    return LegacyTracedModule(f, _force_outplace, return_inputs)(*args, **kwargs)
  File "/localscratch/vincelf.7646845.0/env/lib/python2.7/site-packages/torch/nn/modules/module.py", line 493, in __call__
    result = self.forward(*input, **kwargs)
  File "/localscratch/vincelf.7646845.0/env/lib/python2.7/site-packages/torch/jit/__init__.py", line 295, in forward
    out_vars, _ = _flatten(out)
RuntimeError: Only tuples, lists and Variables supported as JIT inputs, but got OrderedDict
```
## Topics in NVidia Developer Forum
### Existing related topics
* <https://forums.developer.nvidia.com/t/error-while-trying-to-onnx-model-file-to-trt-engine/108145/4>
* <https://forums.developer.nvidia.com/t/tensorrt-5-1-6-1-onnx-parser-cannot-parse-an-onnx-model-produced-by-tf2onnx/107356/2>
* <https://forums.developer.nvidia.com/t/tensorrt-cannot-parse-some-onnx-models-transferred-from-paddle/83299/4>
* <https://forums.developer.nvidia.com/t/some-onnx-layers-cannot-be-parsed-by-tensorrt/83377>
* <https://forums.developer.nvidia.com/t/hello-ai-world-now-supports-python-and-onboard-training-with-pytorch/77762/22>
* <https://forums.developer.nvidia.com/t/convert-pth-to-onnx-on-xavier-int8-calibration/115834/2>

### New topic Trying to regenerate onnx for jetson-nano
<https://forums.developer.nvidia.com/t/trying-to-regenerate-onnx-for-jetson-nano/125494?u=vincelf>

Hi hope all goes well. For the purpose of an essay for the university, I am trying to regenerate the onnx that is provided by NVidia for fcn-resnet18-deepscene-576x320. The objective is to train the model with my own images, but I want first to make sure I can run the onnx I am generating before doing anything else. 
(For your curiosity the essay is an evaluation of the Jetson Nano's capacacity to run inference (semantic segmentation) in realtime with high resolution video. )

I can train and generate the onnx but it gives me the unfamous assertion ```Assertion failed: axis >= 0 && axis < nbDims```. I understand that torch.view needs to be replaced by torch.flatten(). But I am using the code / models provided by NVidia, and checked that the fix is there (torch.view replaced by torch.flatten() ). But I still get the error 
```
ERROR: onnx2trt_utils.hpp:347 In function convert_axis:
[8] Assertion failed: axis >= 0 && axis < nbDims
```

And I do not understand where I fail. 
I already tried many many many many different things, and spent many hours on this ... still no success. 
I am so close to close the loop of my experience to go from training to onnx.  
If someone has a slim idea or suggestion... 
Another important  thing : I am very limited in my environment. It's either the Jetson nano, or a server provided by Compute Canada. No DIGITS. 
And I am far from being an expert (today) with Pytorch, DeepLearning models and ONNX. I am a student. 

Here are all the steps below I am following on a Compute Canada AI/ML server. I do not do that on the Jetson Nano. I tried to export to onnx a simple model on a docker with JetpPack 4.4, but the Jetson nano freezes. (```https://pytorch.org/docs/stable/onnx.html; docker pull nvcr.io/nvidia/l4t-ml:r32.4.2-py3```)


```
# These commands are run on a Compute Canada (server) instance
# move to the home directory
cd ~

# retrieve the deepscene freiburg_forest_multispectral_annotated (1.2Gb); really quick from compute canada (>13Mb/sec)
wget http://deepscene.cs.uni-freiburg.de/static/datasets/freiburg_forest_multispectral_annotated.tar.gz

# untar-zip the deepscene freiburg_forest_multispectral_annotated (1.2Gb) directly inside the home folder
tar xvf freiburg_forest_multispectral_annotated.tar.gz 

module python/2.7 cuda/10.0 cudnn 
# clean the python virtual env
rm -rf $SLURM_TMPDIR/env

# create the virtual env
virtualenv --no-download $SLURM_TMPDIR/env

# activate the python virtual env
source $SLURM_TMPDIR/env/bin/activate

# install requirements
pip install --no-index torch==1.3.0
pip install --no-index scikit-learn
pip install --no-index six
pip install --no-index pillow==6.1.0
pip install --no-index ~/Cython-0.29.17.tar.gz 
pip install --no-index ~/pycocotools-2.0.0.tar.gz

# retrieve the fork for vision-0.3.0 from dusty-nv
cd ~
git clone https://github.com/dusty-nv/vision.git

# retrieve the code for training of semantic segmentation networks with PyTorch for jetson nano
cd ~
git clone https://github.com/dusty-nv/pytorch-segmentation.git

cd ~/vision-0.3.0-dusty-ng
rm -rf build/
python setup.py build install

# run torchvision test models
python test/test_models.py 

# remap the deepscene images for the model
cd ~/pytorch-segmentation-master
python deepscene_remap.py ~/downloads/freiburg_forest_annotated/train/GT_color ~/downloads/freiburg_forest_annotated/train/GT_index
python deepscene_remap.py ~/downloads/freiburg_forest_annotated/train/GT_color ~/downloads/freiburg_forest_annotated/train/GT_index
python deepscene_remap.py ~/downloads/freiburg_forest_annotated/test/GT_color ~/downloads/freiburg_forest_annotated/test/GT_index

# train
cd ~/pytorch-segmentation-master
python train.py -a fcn_resnet18 --dataset deepscene --model-dir ./model_output --dist-url 'tcp://127.0.0.1:5556' /home/vincelf/downloads/freiburg_forest_annotated

# export to onnx
python onnx_export.py --input model_output/model_best.pth --output resnet18-vlf.onnx

# test the onnx with trtexec
/usr/src/tensorrt/bin/trtexec --onnx=/home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx --explicitBatch
[04/14/2020-22:02:03] [I] === Model Options ===
[04/14/2020-22:02:03] [I] Format: ONNX
[04/14/2020-22:02:03] [I] Model: /home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx
[04/14/2020-22:02:03] [I] Output:
[04/14/2020-22:02:03] [I] === Build Options ===
[04/14/2020-22:02:03] [I] Max batch: explicit
[04/14/2020-22:02:03] [I] Workspace: 16 MB
[04/14/2020-22:02:03] [I] minTiming: 1
[04/14/2020-22:02:03] [I] avgTiming: 8
[04/14/2020-22:02:03] [I] Precision: FP32
[04/14/2020-22:02:03] [I] Calibration: 
[04/14/2020-22:02:03] [I] Safe mode: Disabled
[04/14/2020-22:02:03] [I] Save engine: 
[04/14/2020-22:02:03] [I] Load engine: 
[04/14/2020-22:02:03] [I] Inputs format: fp32:CHW
[04/14/2020-22:02:03] [I] Outputs format: fp32:CHW
[04/14/2020-22:02:03] [I] Input build shapes: model
[04/14/2020-22:02:03] [I] === System Options ===
[04/14/2020-22:02:03] [I] Device: 0
[04/14/2020-22:02:03] [I] DLACore: 
[04/14/2020-22:02:03] [I] Plugins:
[04/14/2020-22:02:03] [I] === Inference Options ===
[04/14/2020-22:02:03] [I] Batch: Explicit
[04/14/2020-22:02:03] [I] Iterations: 10 (200 ms warm up)
[04/14/2020-22:02:03] [I] Duration: 10s
[04/14/2020-22:02:03] [I] Sleep time: 0ms
[04/14/2020-22:02:03] [I] Streams: 1
[04/14/2020-22:02:03] [I] Spin-wait: Disabled
[04/14/2020-22:02:03] [I] Multithreading: Enabled
[04/14/2020-22:02:03] [I] CUDA Graph: Disabled
[04/14/2020-22:02:03] [I] Skip inference: Disabled
[04/14/2020-22:02:03] [I] === Reporting Options ===
[04/14/2020-22:02:03] [I] Verbose: Disabled
[04/14/2020-22:02:03] [I] Averages: 10 inferences
[04/14/2020-22:02:03] [I] Percentile: 99
[04/14/2020-22:02:03] [I] Dump output: Disabled
[04/14/2020-22:02:03] [I] Profile: Disabled
[04/14/2020-22:02:03] [I] Export timing to JSON file: 
[04/14/2020-22:02:03] [I] Export profile to JSON file: 
[04/14/2020-22:02:03] [I] 
----------------------------------------------------------------
Input filename:   /home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx
ONNX IR version:  0.0.4
Opset version:    9
Producer name:    pytorch
Producer version: 1.3
Domain:           
Model version:    0
Doc string:       
----------------------------------------------------------------
WARNING: ONNX model has a newer ir_version (0.0.4) than this parser was built against (0.0.3).
WARNING: Your ONNX model has been generated with INT64 weights, while TensorRT does not natively support INT64. Attempting to cast down to INT32.
Successfully casted down to INT32.
While parsing node number 2 [Gather]:
ERROR: onnx2trt_utils.hpp:347 In function convert_axis:
[8] Assertion failed: axis >= 0 && axis < nbDims
[04/14/2020-22:02:18] [E] Failed to parse onnx file
[04/14/2020-22:02:18] [E] Parsing model failed
[04/14/2020-22:02:18] [E] Engine could not be created
&&&& FAILED TensorRT.trtexec # /usr/src/tensorrt/bin/trtexec --onnx=/home/lefv2603/projects/dusty-nv/pytorch-segmentation-master/resnet18-vlf.onnx --explicitBatch
```
