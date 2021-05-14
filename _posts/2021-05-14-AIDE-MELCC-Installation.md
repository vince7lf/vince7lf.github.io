Suivre les indications <https://github.com/microsoft/aerial_wildlife_detection/blob/master/doc/install.md>

Avec les indications supplémentaires suivantes: 

+ créer le répertoire d'installation ($targetDir)
+ cd targetDir avant de faire le `git clone`
+ cd aerial_wildlife_detection avant de faire le `pip install requirements.txt`

# Prérequis

## Install rust Ubuntu 18.04 

Sinon il y aura une erreur à l'installation des `requirements.txt`

```
This package requires Rust >=1.41.0.
```

Reference : <https://www.cloudbooklet.com/install-rust-on-ubuntu-18-04-lts/>

```
sudo apt update
sudo apt upgrade

sudo apt install curl

curl https://sh.rustup.rs -sSf | sh
```

### Erreur à l'installation de rust:
```
info: installing component 'rust-std'
 11.2 MiB /  22.9 MiB ( 49 %)   7.1 MiB/s in  5s ETA:  1smemory allocation of 16777216 bytes failed
Aborted (core dumped)
```
