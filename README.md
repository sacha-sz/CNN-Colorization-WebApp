# DL-UPM - Colorisation d'images par réseau de neurones convolutifs

Ce dépôt contient les travaux réalisés dans le cadre du cours de **Deep Learning** à l'**UPM** (Universidad Politécnica de Madrid). Le projet porte sur la **colorisation automatique d'images en niveaux de gris** à l'aide d'un réseau de neurones convolutifs (CNN) entraîné sur des images de la NASA Astronomy Picture of the Day.

Une application web interactive permet de tester le modèle directement depuis un navigateur.

<br/>

## Vue d'ensemble

| Composant | Description | Fichier |
|-----------|-------------|---------|
| Modèle CNN | Architecture encodeur-décodeur avec skip connections et couches dilatées | [`streamlit.py`](streamlit.py) |
| Application web | Interface Streamlit pour la colorisation en ligne | [`streamlit.py`](streamlit.py) |
| Déploiement | Conteneurisation Docker de l'application | [`Dockerfile`](Dockerfile) |
| Dépendances | Bibliothèques Python requises | [`requirements.txt`](requirements.txt) |

<br/>

## Architecture du modèle

Le modèle suit une architecture de type **U-Net** adaptée à la colorisation d'images. Il prend en entrée un canal de luminance (L) et prédit les deux canaux de chrominance (a, b) dans l'espace colorimétrique **LAB**.

---

### Encodeur

| Couche | Entrée | Sortie | Stride |
|--------|--------|--------|--------|
| Conv1 + BN | 1 | 32 | 2 |
| Conv2 + BN | 32 | 64 | 2 |
| Conv3 + BN | 64 | 128 | 2 |
| Conv4 + BN | 128 | 256 | 2 |
| Conv5 dilatée + BN | 256 | 256 | 1 (dilation=2) |
| Conv6 dilatée + BN | 256 | 256 | 1 (dilation=2) |

---

### Décodeur (avec skip connections)

| Couche | Entrée | Sortie | Stride |
|--------|--------|--------|--------|
| TConv1 + BN | 256 | 128 | 2 |
| TConv2 + BN | 256 (cat x3) | 64 | 2 |
| TConv3 + BN | 128 (cat x2) | 32 | 2 |
| TConv4 | 64 (cat x1) | 2 | 2 |
| Conv de sortie | 3 (cat L) | 2 | 1 |

<br/>

## Application web

L'interface Streamlit permet de :

- Uploader une ou plusieurs images (JPG, PNG)
- Redimensionner automatiquement les images à 400x400 pixels
- Coloriser les images via le modèle CNN
- Comparer côte à côte l'image originale en niveaux de gris et l'image colorisée
- Télécharger les images colorisées

<br/>

## Utilisation

### Via Docker (recommandé)

```bash
# Cloner le dépôt
git clone https://github.com/sacha-sz/DL-UPM.git
cd DL-UPM

# Construire l'image Docker
docker build -t dl-upm .

# Lancer le conteneur
docker run -p 8501:8501 dl-upm
```

L'application est ensuite accessible à l'adresse : `http://localhost:8501`

**Prérequis** : [Docker Desktop](https://www.docker.com/products/docker-desktop/) installé sur la machine.

---

### En local

```bash
# Cloner le dépôt
git clone https://github.com/sacha-sz/DL-UPM.git
cd DL-UPM

# Installer les dépendances
pip install -r requirements.txt

# Lancer l'application
streamlit run streamlit.py
```

<br/>

## Technologies utilisées

- **Python 3.12** - langage principal
- **PyTorch** - construction et inférence du réseau de neurones
- **Streamlit** - interface web interactive
- **OpenCV** - traitement et redimensionnement des images
- **scikit-image** - conversion de l'espace colorimétrique LAB vers RGB
- **Pillow** - manipulation des images
- **Docker** - conteneurisation de l'application

<br/>

## Auteurs

- **[@baneboll11](https://github.com/baneboll11)**
- **[@Sim089n](https://github.com/Sim089n)**
- **[@sacha-sz](https://github.com/sacha-sz)**

<br/>

## Références

- [NASA Astronomy Picture of the Day](https://apod.nasa.gov/apod/astropix.html)
- [Streamlit Documentation](https://docs.streamlit.io/)
- [PyTorch Documentation](https://pytorch.org/docs/)
