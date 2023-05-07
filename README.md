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

Avant de procéder à la création de notre librarie Julia, on va configurer `git` pour y ajouter nos informations personelles. 

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

Il existe une fonction dans `Pkg` qui permet de générer facilement la structure de base d'une librairie Julia. On va s'en servir pour créer une libraire bidon appelée `NewPackage.jl` pour l'exemple : 

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

Plus tard, lorsqu'on ajoutera des dépendances à notre librairie, elles seront listées dans ce fichier.

Le fichier de module `src/NewPackage.jl` va être celui où on charge les dépendances, inclut les fichiers de fonctions et exporte les fonctions de notre librairie. Pour le moment, il ne contient qu'une fonction `greet()` générée par défaut par `Pkg` :

```
module NewPackage

greet() = print("Hello World!")

end # module NewPackage
```

On peut tester cette fonction en chargeant notre librairie. Puisqu'elle n'est pas encore installée, il faut activer son environnement avant de la charger :

```julia
julia> ]
pkg> activate .
julia> using NewPackage
```

Puisque la fonction `greet()` n'est pas exportée, il faut ajouter le nom de la librairie devant lorsqu'on l'appelle :

```julia
julia> NewPackage.greet()
```


### Suivi des modifications avec `git`

#### Initialisation 

Pour activer le suivi des modifications avec `git`, il faut que `git` soit initialisé dans le répertoire de la librairie :

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

Les changements apportés devraient être visibles par `git` :

```
$ git status
```

La description du changement peut être lue dans le log :

```
$ git log
```

### Synchronisation des changements avec GitHub

On aimerait pouvoir envoyer nos fichiers vers GitHub pour les partager avec le monde. Avant cela, il faut configurer notre compte pour qu'il reconnaisse notre ordinateur.

#### Génération de la clé SSH

On génère une clé SSH :

```bash
$ ssh-keygen -t ed25519 -C "gabriel.gobeil199@gmail.com"
$ eval "$(ssh-agent -s)"
$ ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

On copie la clé dans notre presse-papier afin de la recopier dans le champ approprié sur GitHub :

```bash
$ pbcopy < ~/.shh/id_ed25519.pub
```

#### Configuration sur *GitHub.com*

On doit se rendre sur GitHub dans les paramètres à la section **SSH and GPG keys** : [https://github.com/settings/keys](https://github.com/settings/keys).

On clique sur le bouton vert à droite *New SSH key* puis on nomme la clé (p. ex., *mac-gab*), colle le contenu du presse-papier dans le champ **key** et clique sur le bouton vert *Add SSH key*.

#### Création du dépot à distance 

Maintenant que la clé SSH de notre ordinateur est lié à notre compte GitHub, on va créer le répertoire associé à notre librairie en se rendant sur [https://github.com/new](https://github.com/new).

Dans le champ **Repository name**, on va entrer le nom de notre librairie : *NewPackage.jl*.

Puisqu'on va syncroniser un répertoire existant, on laisse la section **Initialize this repository with:** vide, on va ajouter le fichier README plus tard.

Une fois le répertoire créé sur GitHub, on peut syncroniser notre dépôt local avec celui à distance :

```bash
$ git remote add origin git@github.com:houton199/NewPackage.jl.git
$ git push -u origin master
```

---
## 2. Développement de la librairie

On a maintenant la librairie Julia la plus basique qui soit, libre à nous de la développer à notre guise ! Dans cet exemple, nous allons ajouter le fichier *README* et des dépendances à notre librairie.

### Ajout du README

L'ajout d'un *README* va permettre de décrire notre librairie à ses potentiels utilisateurs. On va créer le fichier README.md à même le terminal avec `touch` puis utiliser l'éditeur `vi` pour le remplir :

```bash
$ touch README.md
$ vi README.md
```

Dans l'éditeur `vi`, pour écrire du texte, on doit entrer de le mode insertion avec la touche <kbd>i</kbd>.

On ajoute quelques lignes pour décrire le projet :

```
# NewPackage.jl

Une librairie Julia exemplaire...
```

On peut aussi ajouter un [badge](https://www.repostatus.org/) pour l’état du projet : [![Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](https://www.repostatus.org/badges/latest/wip.svg)](https://www.repostatus.org/#wip)

```
# NewPackage.jl

[![Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](https://www.repostatus.org/badges/latest/wip.svg)](https://www.repostatus.org/#wip)

Une librairie Julia exemplaire...
```





Pour quitter `vi`, il faut quitter le mode insertion avec <kbd>ESC</kbd> et taper `:wq` + <kbd>Enter</kbd> (w pour *write*, q pour *quit*).


#### Commit et synchronisation avec le dépôt à distance

Une fois qu'on est satisfait des ajouts au *README*, on peut faire un commit et l'envoyer sur GitHub :

```bash
$ git add README.md
$ git commit -m "Ajout du README"
$ git push origin master
```

Notez qu'on n'a plus besoin du `-u` ici car la branche existe déjà sur le répertoire distant.

### Ajout de dépendances

On va ajouter des dépendances à notre librairie en les installant dans son environnement.

#### Activation de l'environnement 

Pour activer l'environnement de notre librairie avec `Pkg`, on utilise la fonction `activate` :

```julia
julia> ]
pkg> activate .
```

On peut vérifier qu'on est dans le bon environnement avec `status` :

```julia
pkg> status
```

Il devrait être écrit qu'on se trouve dans `Project NewPackage v0.1.0`. Puisqu'aucune dépendance n'est installée pour le moment, la liste est vide.

#### Installation des dépendances

Les dépendances s'ajoutent de la même façon qu'on installe n'importe quelle librairie Julia avec `Pkg` :

```julia
pkg> add Distributions
```

Une fois la librairie installée, on peut aller voir si elle se trouve dans le fichier de projet `Project.toml` :

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

### Ajout d'un fichier de fonctions

Pour l'instant, notre librairie n'est constituée que du fichier `src/NewPackage.jl`. Nous allons créer le fichier de fonction `functions.jl`et l'ajouter à la librairie :

```
$ touch src/functions.jl
```

On va l'ouvrir dans l'éditeur `vi` et y déplacer la fonction `greet()` contenue dans `src/NewPackage.jl`.

Le fichier `src/functions.jl` devrait ressembler à ça :

```
greet() = print("Hello World!")
```

et le fichier `src/NewPackage.jl` à ça :

```
module NewPackage

end # module NewPackage
```

#### Inclure des fichiers au module

Pour inclure le nouveau fichier `functions.jl` à notre librairie, on va l'ajouter au module avec `include()`:

```
module NewPackage

include("functions.jl");

end # module NewPackage
```

#### Exporter des fonctions

On peut exporter la fonction `greet()` pour y avoir accès sans devoir écrire `NewPackage.` devant à chaque fois :

```
module NewPackage

include("functions.jl");

export greet

end # module NewPackage
```

#### Charger des dépendances

C'est aussi dans ce fichier qu'on doit charger les dépendances si nécessaire :

```
module NewPackage

using Distributions

include("functions.jl");

export greet

end # module NewPackage
```

*Pour un exemple de comment structurer le répertoire source d'une librairie Julia, on peut s'inspirer de la structure d'[Extremes.jl](https://github.com/jojal5/Extremes.jl/tree/master/src) et de son fichier de [module](https://github.com/jojal5/Extremes.jl/blob/master/src/Extremes.jl).*

#### Commit et synchronisation avec le dépôt à distance

Une fois qu'on est satisfait des ajouts, on peut faire un commit et l'envoyer sur GitHub :

```bash
$ git add src/functions.jl
$ git commit -m "Ajout du fichier de fonctions"
$ git push origin master
```

---
## 3. Tests

Pour augmenter la confiance envers notre nouvelle librairie Julia, il est important de tester ses fonctionnalités.

La bonne pratique suggère *une fonction, un test*. On peut ainsi structurer nos tests de façon miroir avec la structure des fonctions.

On va créer un dossier *test* avec le fichier *runtests.jl* qui va inclure le fichier de test pour le fichier *functions.jl*.

```
NewPackage.jl/
├── Project.toml
├── README.md
├── src
│   └── NewPackage.jl
└── test
    ├── functions_test.jl
    └── runtests.jl
```

Il ne faut pas oublier d’inclure la libraire Test au fichier de projet.
 

---
## 4. Intégration continue

On aimerait automatiser le processus de test pour vérifier que l’ajout de nouvelles fonctionnalités à notre librairie ne viennent pas tout briser. Également, ça serait intéressant d’afficher le taux de couverture de notre librairie, soit le pourcentage de ligne de code qui est couvert par des tests.

### GitHub Workflows

On va se servir de GitHub pour lancer des processus automatiques qui vont nous informer si la librairie compile (*build status*) et quel est sa couverture avec Codecov.

On doit d’abord créer un nouveau dossier caché `.github` dans lequel on va ajouter le dossier `workflows`. Ce dossier va être celui dans lequel on place les instructions pour dire à GitHub quoi faire lorsqu’on push quelque chose de nouveau sur une branche de notre répertoire.

```
$ mkdir -p .github/workflows
```

On va y insérer le fichier d’instructions `ci.yml` :

```
$ touch .github/workflows/ci.yml
```

Ici, je vais reprendre les instructions déjà écrites pour Extremes.jl mais les instructions sont relativement simple à comprendre.


```
name: CI
on:
  pull_request:
    branches:
      - master
      - dev
  push:
    branches:
      - master
      - rm_Manifest_toml
    tags: '*'
jobs:
  test:
    name: Julia ${{ matrix.version }} - ${{ matrix.os }} - ${{ matrix.arch }} - ${{ github.event_name }}
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        version:
          - '1'
        os:
          - ubuntu-latest
        arch:
          - x64
    steps:
      - uses: actions/checkout@v2
      - uses: julia-actions/setup-julia@v1
        with:
          version: ${{ matrix.version }}
          arch: ${{ matrix.arch }}
      - uses: actions/cache@v1
        env:
          cache-name: cache-artifacts
        with:
          path: ~/.julia/artifacts
          key: ${{ runner.os }}-test-${{ env.cache-name }}-${{ hashFiles('**/Project.toml') }}
          restore-keys: |
            ${{ runner.os }}-test-${{ env.cache-name }}-
            ${{ runner.os }}-test-
            ${{ runner.os }}-
      - uses: julia-actions/julia-buildpkg@v1
      - uses: julia-actions/julia-runtest@v1
      - uses: julia-actions/julia-processcoverage@v1
      - uses: codecov/codecov-action@v2
```

### Couverture de code avec CodeCov

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

- https://github.com/JuliaRegistries/Registrator.jl
