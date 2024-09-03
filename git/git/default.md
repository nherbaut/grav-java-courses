# Git

[Git](https://git-scm.com) est un gestionnaire de contrôle de source
(Source Control Management ou SCM). Il permet de partager efficacement
les fichiers source d’un projet. Pour ce cours, [Git](https://git-scm.com) va nous permettre de
partager et de suivre les modifications du code source de nos
applications et la configuration de nos machines cibles. Un système de
contrôle de version tel que [Git](https://git-scm.com) est fondamental pour permettre à une
équipe de travailler collectivement sur une base de code commune. [Git](https://git-scm.com)
est avant tout conçu gérer des fichiers en mode texte.

[Git](https://git-scm.com) a été originellement développé par Linus Torvalds pour gérer les
évolutions du code source du noyau Linux.

## Installation

[Git](https://git-scm.com) est disponible dans tous les dépôts de logiciels des distributions
Linux. Pour une distribution basée sur Debian (Ubuntu, Mint…), il
suffit de taper en ligne de commande :

```shell
sudo apt install git
```

Pour Mac, vous pouvez télécharger l’installeur à l’adresse :
[https://git-scm.com/download/mac](https://git-scm.com/download/mac)

Pour Windows, vous pouvez télécharger l’installeur à l’adresse :
[https://git-scm.com/download/win](https://git-scm.com/download/win)

## Configuration

Avant votre première utilisation, il est conseillé de configurer [Git](https://git-scm.com) en
indiquant l’identité que vous utiliserez. Pour cela vous devez définir
votre nom et votre email avec les commandes :

```shell
git config --global user.name "VOTRE_NOM"
git config --global user.email "VOTRE_EMAIL"
```

Si vous avez un éditeur de texte préféré, vous pouvez également donner
son chemin avec la commande

```shell
git config --global core.editor "CHEMIN DE L'EDITEUR"
```

La commande git config permet de modifier la configuration de votre
projet ou la configuration globale si l’option `--global` est
présente. La configuration globale est sauvegardée dans le fichier
`~/.gitconfig` et pour chaque projet dans le fichier `.git/config`.
Vous pouvez vérifier le contenu de votre configuration grâce à la
commande :

```shell
git config -l
```

## Initialisation d’un dépôt

Si vous partez d’un répertoire dont vous désirez suivre les changements
avec [Git](https://git-scm.com), vous initialisez votre dépôt local en vous positionnant dans
le répertoire souhaité et tapant la commande :

```shell
git init
```

Cette commande crée un répertoire caché `.git` qui va contenir
l’ensemble des informations permettant à [Git](https://git-scm.com) de suivre les
modifications.

Si vous disposez d’une URL d’un dépôt [Git](https://git-scm.com), vous pouvez le cloner pour
commencer à modifier son contenu localement grâce à la commande :

```shell
git clone <URL> [<répertoire>]
```

Vous pouvez éventuellement désigner le répertoire à créer qui contiendra
le résultat du clone sinon [Git](https://git-scm.com) le déduit du contenu de l’URL.

## Suivi des modifications du dépôt

À tout moment, vous pouvez utiliser la commande

```shell
git status
```

Cette commande vous permet de connaître l’état des fichiers qui ont
évolué par rapport à votre dépôt local.

Pour [Git](https://git-scm.com), un fichier peut avoir quatre états :

Untracked (non suivi) :
: Le fichier existe sur le disque mais ne fait pas partie du dépôt.

Unmodified (non modifié) :
: Le fichier existe sur le disque et dans le dépôt et ce fichier n’a
  pas été modifié localement. La commande `git status` ignore les
  fichiers dans cet état.

Modified (modifié) :
: Le fichier existe sur le disque et dans le dépôt et ce fichier a été
  modifié.

Staged :
: Ce fichier est dans un état différent par rapport au dépôt. Il peut
  avoir été modifié, ajouté ou supprimé. Et l’utilisateur l’a ajouté à
  la **staging area**. Dans ce cas, les modifications seront
  incorporées au dépôt au prochain commit.

[Git](https://git-scm.com) ne connaît pas directement la notion de répertoire. Il ne suit que
les fichiers et les liens symboliques. Un répertoire existe pour [Git](https://git-scm.com) dès
qu’il contient un fichier et, à l’opposé, un répertoire n’existe plus
dès qu’il ne contient plus de fichier ou de sous répertoire contenant
eux-mêmes des fichiers.

Les commandes ci-dessous permettent de gérer les états d’un fichier :

```shell
git add <fichier/répertoire>
```

Permet d’ajouter le fichier à la **staging area**. S’il s’agit d’un
nouveau fichier ou s’il a été modifié, le fichier passe à l’état Staged
sinon cette commande est sans effet.

Pour ajouter tous les fichiers modifiés à la staging area :

```shell
git add .
```

Pour ajouter tous les fichiers modifiés et tous les fichiers créés à la
staging area :

```shell
git add -A .
```

```shell
git rm <fichier>
```

Permet d’ajouter le fichier à la staging area en demandant sa
suppression. Le fichier passe à l’état Staged et est supprimé du
répertoire de travail.

```shell
git reset -- <fichier/répertoire>
```

Permet de retirer de la staging area un fichier qui y a été ajouté.

```shell
git checkout <fichier/répertoire>
```

Permet d’annuler les modifications locales d’un fichier. Le fichier ne
doit pas être dans la staging area (utilisez git reset pour l’en
sortir). Le checkout annule les modifications et les suppressions.

```shell
git diff <fichier/répertoire>
```

Permet de visualiser les différences entre les modifications locales et
le dépôt local.

```shell
git diff --staged <fichier/répertoire>
```

Permet de visualiser les différences entre les modifications dans la
staging area et le dépôt local.

## Validation des modifications

Une fois que vous êtes satisfaits des modifications que vous avez
apportées à vos fichiers, il vous suffit de les ajouter à la staging
area et d’utiliser la commande commit :

```shell
git commit -m "un message explicatif"
```

L’option -m permet de passer un message explicatif des modifications. Si
vous n’ajoutez pas cette option, [Git](https://git-scm.com) ouvrira un éditeur de texte pour
vous permettre de saisir ce message. La commande commit permet d’ajouter
les modifications de la staging area au dépôt local.

Pour suivre l’historique des opérations de commit, vous pouvez utiliser
la commande :

```shell
git log
```

#### NOTE
[Git](https://git-scm.com) vous permet de créer des alias, c’est-à-dire de définir vos propres
commandes personnalisées et de les sauvegarder dans la configuration de
votre dépôt local ou même de votre configuration globale. Essayez :

```shell
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

Vous aurez ainsi accès à l’alias lg. En tapant :

```shell
git lg
```

[Git](https://git-scm.com) vous affiche maintenant un historique moins verbeux et plus élégant.

## Utilisation d’un dépôt distant

[Git](https://git-scm.com) vous permet de connecter votre dépôt local à un ou plusieurs dépôt
distants (appelés remote repositories). Il devient possible de
synchroniser votre dépôt local avec eux afin de partager vos
modifications et d’intégrer les modifications effectuées par d’autres.
[Git](https://git-scm.com) est un système décentralisé dans la mesure ou il ne présuppose pas
l’existence d’un dépôt de référence. Dans la pratique, il existe souvent
un dépôt distant privilégié que l’on nomme par convention *origin*.

Si vous créez un dépôt local par clonage (`git clone ...`) alors cette
opération a pour résultat de créer un dépôt local rattaché au dépôt
d’origine justement nommé *origin*. Si vous avez initialisé un dépôt
local (`git init`) alors vous pouvez le rattacher à un dépôt distant à
tout moment.

Pour connaître la liste des dépôts distants associés à votre dépôt
local, vous utilisez la commande :

```shell
git remote -v
```

Pour associer un dépôt distant à votre dépôt local, vous utilisez la
commande :

```shell
git remote add <nom du dépôt> <URL du dépôt>
```

Vous êtes libres de choisir le nom que vous désirez mais par convention,
on utilise *origin* pour désigner le dépôt distant qui est à l’origine
du dépôt local.

Pour récupérer les modifications du dépôt distant, utilisez la
commande :

```shell
git pull <nom du dépôt> master
```

Pour envoyer les évolutions de votre dépôt local dans le dépôt distant :

```shell
git push <nom du dépôt> master
```

Dans les commandes ci-dessus, *master* désigne le nom de la branche. Un
branche permet d’isoler les modifications afin d’avoir simultanément
plusieurs étapes de développement des fichiers. Nous verrons plus en
détail cette notion plus tard. Retenez pour l’instant que, par
convention, la branche par défault est la plupart du temps appelé
`master`.

Les opérations de pull/push peuvent générer des fusions et des conflits
si les mêmes fichiers sont modifiés par des utilisateurs différents.
Dans ce cas, [Git](https://git-scm.com) arrête l’opération en cours et demande à l’utilisateur
de résoudre les conflits.

## Exclusion de fichiers

La plupart du temps, il n’est pas souhaitable d’ajouter des fichiers au
dépôt [Git](https://git-scm.com). Il s’agit le plus souvent de fichiers résultants de la
compilation ou de tout procédé de génération. Ils peuvent donc être
facilement recréés. Un dépôt [Git](https://git-scm.com) n’oublie rien ! Même si vous supprimez
un fichier, ce dernier est toujours présent dans des versions
antérieures et donc il est toujours conservé quelque part dans le dépôt.
Il est donc fortement conseillé de n’ajouter à un dépôt que les fichiers
nécessaires. Vous pouvez créer un fichier **\`\`.gitignore\`\`** dans votre
répertoire de travail. Ce fichier permet de lister tous les
fichiers/répertoires que [Git](https://git-scm.com) doit totalement ignorer.

```text
# fichiers temporaires pour Maven
target

# fichiers de configuration Eclipse
.classpath
.project
.settings

# fichiers de configuration Itellij IDEA
.idea
*.iml
```

## Git Hub

[Git Hub](https://github.com) est un site permettant de créer et
d’héberger des dépôts [Git](https://git-scm.com) distants. Son utilisation est gratuite pour
les dépôts libres de droit. Si ce n’est pas déjà fait, créez-vous un
compte afin de pouvoir ultérieurement partager vos projets.

## Références

- Documentation officielle : [https://git-scm.com/doc](https://git-scm.com/doc)
- Pro Git : [https://git-scm.com/book/en/v2](https://git-scm.com/book/en/v2)
- Le tutoriel de Git Hub :
  [https://guides.github.com/activities/hello-world/](https://guides.github.com/activities/hello-world/)
- Learn Git Branches (tutoriel visuel pour comprendre les branches) : [https://learngitbranching.js.org/](https://learngitbranching.js.org/)

## Exercices
