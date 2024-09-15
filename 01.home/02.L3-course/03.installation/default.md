---
content:
    items: '@self.listing'
feed:
    limit: 10
partials:
    breadcrumbs:
        toggle: false
    header_subtitle:
        toggle: true
    metadata:
        where: header
process:
    markdown: true
    twig: false
show_sidebar: false
sitemap:
    lastmod: '22-08-2024 13:32'
title: Installation
---

# Installation

## Nouveauté 2024

Utilisation des dev containers dans vscode, suivez [la nouvelle procédure ](/home/installation-de-lenvironnement-de-travail-java)

## Installation Native

Pour suivre ce cours, vous aurez besoin d’un environnement de
développement Java. L’installation du JDK dépend de votre plate-forme :
il est distribué sous la forme d’un installeur pour Windows et MacOS,
et sous la forme d’un package ou d’une archive sous Linux.

#### NOTE
Attention
Votre machine dispose déjà très certainement d’un environnement
d’exécution Java (**JRE**) qui ne contient pas les outils nécessaires
pour développer en Java. Vous devez donc installer un JDK.

## Installation Windows

<iframe src="https://mediatheque.univ-paris1.fr/video/3269-l3-miage-installation-outils-windows/?is_iframe=true" width="640" height="360" style="padding: 0; margin: 0; border:0" allowfullscreen ></iframe>
1. Se rendre sur le site de chocolatey et l’installer
2. Installer les package maven, openjdk et vscode

```bash
choco install maven, openjdk vscode
```

## Installation Mac

<iframe src="https://mediatheque.univ-paris1.fr/video/3268-l3-miage-installation-outils-macos/?is_iframe=true" width="640" height="360" style="padding: 0; margin: 0; border:0" allowfullscreen ></iframe>
1. Se rendre sur le site de home brew et l’installer
2. installer les packages maven, openjdk@21 visual-studio-code

```bash
brew install maven  openjdk@21 visual-studio-code 
```

## Installation Ubuntu

#### NOTE
You probably know how to, but just in case.

```bash
sudo apt update && apt install maven openjdk-21-jdk
wget "https://code.visualstudio.com/sha/download?build=stable&os=linux-deb-x64" -O vscode.deb && sudo dpkg --install ./vscode.deb
```