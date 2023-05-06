# Tutoriel : librairie en Julia

*Ce guide présente les étapes pour créer une librairie Julia de A à Z, c'est-à-dire toutes les étapes permettant d’avoir un projet complet de qualité professionnelle !* 👀

#### Table des matières
1. [Structure de base](#1-structure-de-base)
2. [Développement de la librairie](#2-développement-de-la-librairie)
3. [Tests](#3-tests)
4. [Intégration continue](#4-intégration-continue)
5. [Documentation](#5-documentation)
6. [Enregistrement au registre de Julia](#6-enregistrement-au-registre-de-julia)

---

## 1. Structure de base

### Configuration de git

Avant de procéder à la création de notre librarie julia, on va configurer `git` pour y ajouter nos informations personelles. 

On vérifie d'abord que `git` est bien installé en regardant sa version :

```bash
$ git --version
```

Ensuite, la commande `config --list` permet de lister notre configuration actuelle :

```bash
$ git config --list
```

Puis, pour y ajouter notre nom et notre courriel, on entre les commandes suivantes : 

```bash
$ git config --global user.name "Gabriel Gobeil"
$ git config --global user.email gabriel.gobeil199@gmail.com
```

### Génération d'une librairie de base avec Pkg

Il existe une fonction dans `Pkg` qui permet de générer facilement la structure de base d'une librairie julia :

```julia
julia> ]
pkg> generate NewPackage.jl
```

Cela va créer la structure de base suivante :

```
NewPackage.jl/
├── Project.toml
└── src
    └── NewPackage.jl
```

où `src/NewPackage.jl` est le fichier de module et `Project.toml` le fichier de configuration de la librairie.

Ce dernier fichier contient le nom de la librairie, son numéro d'identification unique, son numéro de version ainsi que les informations sur l'auteur, obtenus à partir de votre configuration `git` :

```
name = "NewPackage"
uuid = "11071f02-195a-4744-98d5-929847dbbb65"
authors = ["Gabriel Gobeil <gabriel.gobeil199@gmail.com>"]
version = "0.1.0"
```

### Suivi des modifications avec git

#### Initialisation 

Pour activer le suivi des modifications avec `git`, il faut que git soit initialisé dans le répertoire de la librairie :

```bash
$ git init
```

Cela va créer un dossier caché `.git` nécessaire à `git`.

```
$ git status
```

#### Premier commit

On doit ajouter et commiter nos fichiers :

```bash
$ git add src/NewPackage.jl
$ git add Project.toml
$ git commit -m "Ajout des fichiers"
```

Les changements aportés devraient être visible par `git` :

```
$ git status
```

La description du changement est visible dans le log :

```
$ git log
```

### Synchronisation des changements avec GitHub

On aimerait pouvoir envoyer nos fichiers vers GitHub pour les partager avec le monde. Avant cela, il faut configurer notre compte pour qu'il reconnaisse notre ordinateur.

#### Clé SSH

On génère une clé SSH :

```bash
$ ssh-keygen -t ed25519 -C "gabriel.gobeil199@gmail.com"
$ eval "$(ssh-agent -s)"
$ ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

On copie la clé dans notre presse-papier afin de la recopier dans le champs approprié sur GitHub :

```bash
$ pbcopy < ~/.shh/id_ed25519.pub
```

#### Sur *GitHub.com*

On doit se rendre sur GitHub dans les paramètres à la sectionSSH and GPG keys : [https://github.com/settings/keys](https://github.com/settings/keys).

On clique sur le bouton vert à droite *New SSH key* puis on nomme la clé (p. ex., *mac-gab*), colle le contenu du presse-papier dans le champs **key** et clique sur le bouton vert *Add SSH key*.

#### Synchronisation avec le dépot à distance 

Maintenant que la clé SSH de notre ordinateur est lié à notre compte GitHub, on peut syncroniser notre dépôt local avec celui à distance :

```bash
$ git remote add origin git@github.com:houton199/NewPackage.jl.git
$ git push -u origin master
```

---
## 2. Développement de la librairie

On a maintenant la librairie julia la plus basique qui soit, libre à nous de la développer à notre guise.

### Ajout du README

Un bon départ serait d'ajouter un *README* pour décrire notre librairie à ses potentiels utilisateurs.

```bash
$ touch README.md
$ vi README.md
```

On peut ajouter un badge pour l’état du projet : https://www.repostatus.org/ 

[![Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](https://www.repostatus.org/badges/latest/wip.svg)](https://www.repostatus.org/#wip)

#### Commit et synchronisation avec le dépôt à distance

Une fois qu'on est satisfait des ajouts, on peut faire un commit et l'envoyer sur GitHub :

```bash
$ git add README.md
$ git commit -m "Ajout du README"
$ git push origin master
```

Notez qu'on n'a plus besoin du `-u` ici car la branche existe déjà sur le répertoire distant.

### Ajout de dépendances

On va ajouter des dépendances à notre librairie en les installant dans son environnement.

#### Activation de l'environnement 

```julia
julia> ]
pkg> activate .
```

#### Installation des dépendances

```julia
pkg> add Distributions
```


```
name = "NewPackage"
uuid = "11071f02-195a-4744-98d5-929847dbbb65"
authors = ["Gabriel Gobeil <gabriel.gobeil199@gmail.com>"]
version = "0.1.0"

[deps]
Distributions = "31c24e10-a181-5473-b8eb-7969acd0382f"
```


#### Commit et synchronisation avec le dépôt à distance

Une fois qu'on est satisfait des ajouts, on peut faire un commit et l'envoyer sur GitHub :

```bash
$ git add README.md
$ git commit -m "Ajout de Distributions.jl"
$ git push origin master
```

---
## 3. Tests

- Pour augmenter la confiance envers notre librairie, c’est important de tester ses fonctionnalités.
- La bonne pratique suggère une fonction, un test.
- On peut ainsi structurer nos tests de façon miroir avec la structure des fonctions.
- On va créer un dossier test avec le fichier runtests.jl qui va inclure le fichier de test pour le fichier functions.jl.
- Il ne faut pas oublier d’inclure la libraire Test au fichier de projet.
 

---
## 4. Intégration continue

- On aimerait automatiser le processus de test pour vérifier que l’ajout de nouvelle fonctionnalité ne viennent pas tout briser.
- Également, ça serait intéressant d’afficher la couverture de notre librairie, le pourcentage de ligne de code qui est couvert par des tests.
- Pour ce, on va se servir de GitHub pour lancer des processus automatiques qui vont nous informer si la librairie compile et quel est sa couverture avec Codecov.
- On doit d’abord créer un nouveau dossier caché .github dans lequel on va ajouter un dossier workflows.
- On va y insérer un fichier d’instructions en .yml qui va dire à GitHub quoi faire lorsqu’on push quelque chose de nouveau sur une branche de notre répertoire.
- Ici, je vais reprendre les instructions déjà écrites pour Extremes.jl mais les instructions sont relativement simple à comprendre.
- Pour avoir la couverture avec Codecov, on doit installer l’application dans le répertoire.
- Pour des librairies publiques, c’est pratiquement tout ce qu’il a faire.
- Ici, puisque c’est privé, on va devoir téléverser le rapport sur Codecov pour obtenir les statistiques.
- On fait l’ajout des instructions pour le téléversement au fichier CI.yml.
- Une fois que les tests sont passés, le rapport est disponible sur codecov.io.


---
## 5. Documentation

Maintenant que notre librairie est sur GitHub et qu’elle est testée, nous pouvons ajouter de la documentation pour aider les potentiels utilisateurs à s’en servir. Pour ce faire, nous allons utiliser la librairie *[Documenter.jl](https://github.com/JuliaDocs/Documenter.jl)*.

### Structure

On va construire la structure de la documentation dans notre répertoire en créant d’abord un dossier `docs` dans lequel on va ajouter le dossier `src`, qui va être le dossier dans lequel on va écrire la documentation, et un fichier `make.jl`, qui est le script julia qui va générer la documentation.

---
## 6. Enregistrement au registre de Julia
