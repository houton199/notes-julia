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

```julia
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

On doit ajouter et *commiter* nos fichiers :

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

Maintenant que la clé SSH de notre ordinateur est liée à notre compte GitHub, on va créer le répertoire associé à notre librairie en se rendant sur [https://github.com/new](https://github.com/new).

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

```julia
greet() = print("Hello World!")
```

et le fichier `src/NewPackage.jl` à ça :

```julia
module NewPackage

end # module NewPackage
```

#### Inclure des fichiers au module

Pour inclure le nouveau fichier `functions.jl` à notre librairie, on va l'ajouter au module avec `include()`:

```julia
module NewPackage

include("functions.jl");

end # module NewPackage
```

#### Exporter des fonctions

On peut exporter la fonction `greet()` pour y avoir accès sans devoir écrire `NewPackage.` devant à chaque fois :

```julia
module NewPackage

include("functions.jl");

export greet

end # module NewPackage
```

#### Charger des dépendances

C'est aussi dans ce fichier qu'on doit charger les dépendances si nécessaire :

```julia
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

Pour augmenter la confiance envers notre nouvelle librairie Julia, il est important de tester ses fonctionnalités. Pour ce faire, nous allons ajouter des tests.

La bonne pratique suggère *une fonction, un test*. On peut ainsi structurer nos tests de façon miroir avec la structure des fonctions.

On va ajouter un dossier *test* avec les fichiers *runtests.jl* et *functions_test.jl*, 

```
NewPackage.jl/
├── Project.toml
├── README.md
├── src
│   ├── NewPackage.jl
│   └── functions.jl
└── test
    ├── functions_test.jl
    └── runtests.jl
```

Le fichier *runtests.jl* est celui qui va exécuter les tests. Il va appeler les différents fichiers de tests comme *functions_test.jl*, le fichier de test pour *functions.jl*.

Pour l'instant, on n'a seulement que la fonction `greet()` à tester, Comme c'est un peu difficile de tester si la fonction imprime le bon message, on va écrire une nouvelle fonction qui retourne une valeur numérique, ça va être plus facile à tester pour les besoins de l'exemple.

Dans le fichier `functions.jl`, on va ajouter la fonction suivante :

```julia
function add_ab(a::Float64, b::Float64)

    return a + b
end
```

On va aussi l'ajouter aux exportations de `NewPackage.jl` : 

```julia
module NewPackage

using Distributions

include("functions.jl");

export greet, add_ab

end # module NewPackage
```

On va écrire une fonction de test pour notre nouvelle fonction dans le fichier `functions_test.jl`. Comme la fonciton `add_ab()` fait l'addition des arguments `a` et `b`, on va tester le comportement de la fonction en vérifiant que `2 + 2 = 4` : 

```julia
@testset "functions.jl" begin
    a = 2.0
    b = 2.0
    @test add_ab(a, b) == 4.0
end
```

Ensuite, on va inclure notre série de test (un seul pour l'instant) dans le fichier *runtests.jl*. Il ne faut pas oublier d’aussi inclure la libraire `Test` au fichier de projet :

```julia
using NewPackage
using Test

@testset "NewPackage.jl" begin
    include("functions_test.jl")
end
```

Maintenant qu’on a écrit notre série de test, on peut se servir de Pkg pour les lancer et tester notre librairie :

```
pkg> test NewPackage
```
 
---
## 4. Intégration continue

On aimerait automatiser le processus de test pour vérifier que l’ajout de nouvelles fonctionnalités à notre librairie ne viennent pas tout briser. Également, il serait intéressant d’afficher le taux de couverture de notre librairie, soit le pourcentage de ligne de code qui est couvert par des tests.

### Installation de Codecov

On va se servir de la plateforme *Codecov* pour évaluer la couverture de notre code.

On doit d'abord s'inscrire sur le site : [https://app.codecov.io/signup/](https://app.codecov.io/signup/)

Ensuite, on doit installer l’application dans le répertoire sur GitHub : [https://github.com/apps/codecov](https://github.com/apps/codecov). Pour des librairies publiques, c’est pratiquement tout ce qu’il a faire pour cette étape. 

Si la librairie est privée, on doit récupérer le jeton de téléversement en se rendant sur la page de notre librairie dans Codecov : [https://app.codecov.io/gh](https://app.codecov.io/gh). Le jeton devrait être listé sous la section **Step 1: add repository token as repository secret**, par exemple :

```
CODECOV_TOKEN=f8e477db-dc97-4bec-aa76-3ad49a3815c2
```

On va s'en servir à la prochaine étape pour téléverser le rapport sur Codecov pour obtenir les statistiques.


### GitHub Actions

On va se servir de GitHub Actions pour lancer des processus automatiques qui vont nous informer si la librairie compile (*build status*) et faire les tests puis envoyer le rapport sur Codecov.

On doit d’abord créer un nouveau dossier caché `.github` dans lequel on va ajouter le dossier `workflows`. Ce dossier va être celui dans lequel on place les instructions pour dire à GitHub quoi faire lorsqu’on *push* quelque chose de nouveau sur une branche de notre répertoire, c'est ce qui va lancer nos tests.

```
$ mkdir -p .github/workflows
```

On va y insérer le fichier d’instructions `ci.yml` :

```
$ touch .github/workflows/ci.yml
```

On va insérer les lignes suivantes : 

```yml
name: CI
on: [push, pull_request]
jobs:
  run:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Set up Julia 1.8.0
        uses: julia-actions/setup-julia@v1
        with:
          version: "1.8.0"
      - uses: julia-actions/julia-buildpkg@v1
      - uses: julia-actions/julia-runtest@v1
      - uses: julia-actions/julia-processcoverage@v1
      - uses: codecov/codecov-action@v3
```

Ces instructions sont relativement simples à comprendre, en gros c'est des *jobs* qui sont lancés à chaque *push* et à chaque *pull_request*. 

Si la librairie est privée, on doit ajouter une *job* de plus pour téléverser le rapport sur Codecov. C'est ici que le `CODECOV_TOKEN` sera nécessaire, il faut remplacer `${CODECOV_TOKEN}` par notre jeton, par exemple, `f8e477db-dc97-4bec-aa76-3ad49a3815c2` dans mon cas :

```
      - name: Upload coverage reports to Codecov
      run: |
        curl -Os https://uploader.codecov.io/latest/linux/codecov
        chmod +x codecov
        ./codecov -t ${CODECOV_TOKEN}
```

### Badges d'état et de couverture

Pour afficher le taux de couverture calculé par Codecov, on va ajouter le badge de couverture au *README* ainsi que le badge d’état de GitHub Actions pour le *build*.

#### Codecov

Sur Codecov, le code MarkDown pour générer le badge se trouve sur la page de la librairie dans la section **Badges & Graphs** de l'onglet **[Settings](https://app.codecov.io/gh/houton199/NewPackage.jl/settings/badge)** :

```
[![codecov](https://codecov.io/gh/houton199/ExtendedExtremes.jl/branch/master/graph/badge.svg?token=7UGVMF0ENE)](https://codecov.io/gh/houton199/ExtendedExtremes.jl)
```

On va l'ajouter au README sous le badge d'état de la librairie : 

```
# NewPackage.jl

[![Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](https://www.repostatus.org/badges/latest/wip.svg)](https://www.repostatus.org/#wip)
[![codecov](https://codecov.io/gh/houton199/ExtendedExtremes.jl/branch/master/graph/badge.svg?token=7UGVMF0ENE)](https://codecov.io/gh/houton199/ExtendedExtremes.jl)

Une librairie Julia exemplaire...
```

Le badge devrait apparaître après avoir *pushé* les ajouts au README : [![codecov](https://codecov.io/gh/houton199/ExtendedExtremes.jl/branch/master/graph/badge.svg?token=7UGVMF0ENE)](https://codecov.io/gh/houton199/ExtendedExtremes.jl).


#### GitHub Actions 

Le badge de status produit par GitHub Actions sur trouve sur GitHub.com dans l'onglet **Actions**. Dans la liste à gauche devrait apparaître le nom du workflow donné dans le fichier d'instructions en .yml, *CI*. On le sélectionne puis à droite, en cliquant sur <kbd>...</kbd>, on a l'option *Create status badge*. On récupère le code du badge avec *Copy status badge MarkDown* :

```
[![CI](https://github.com/houton199/NewPackage.jl/actions/workflows/ci.yml/badge.svg)](https://github.com/houton199/NewPackage.jl/actions/workflows/ci.yml)
```

On va l'ajouter au README sous les autres badges de la librairie : 

```
# NewPackage.jl

[![Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](https://www.repostatus.org/badges/latest/wip.svg)](https://www.repostatus.org/#wip)
[![codecov](https://codecov.io/gh/houton199/ExtendedExtremes.jl/branch/master/graph/badge.svg?token=7UGVMF0ENE)](https://codecov.io/gh/houton199/ExtendedExtremes.jl)
[![CI](https://github.com/JuliaExtremes/ExtendedExtremes.jl/actions/workflows/ci.yml/badge.svg)](https://github.com/JuliaExtremes/ExtendedExtremes.jl/actions/workflows/ci.yml)

Une librairie Julia exemplaire...
```

Le badge devrait apparaître après avoir *pushé* les ajouts au README : [![CI](https://github.com/JuliaExtremes/ExtendedExtremes.jl/actions/workflows/ci.yml/badge.svg)](https://github.com/JuliaExtremes/ExtendedExtremes.jl/actions/workflows/ci.yml).




---
## 5. Documentation

Maintenant que notre librairie est sur GitHub et qu’elle est testée, nous pouvons ajouter de la documentation pour aider les potentiels utilisateurs à s’en servir. Pour ce faire, nous allons utiliser la librairie *[Documenter.jl](https://github.com/JuliaDocs/Documenter.jl)*.

### Structure

On va commencer par construire la structure de notre documentation. Dans le répertoire racine de notre librairie, on créer d’abord un dossier `docs` dans lequel on va ajouter le dossier `src`, qui sera le dossier dans lequel on va écrire la documentation, et un fichier `make.jl`, qui sera le script Julia qui va générer la documentation avec *Documenter.jl*.

```
docs/
├── src/
└── make.jl
```

Le dossier `src` va contenir les fichiers MarkDown des pages du site de la documentation. Lorsqu'on va exécuter le script `make.jl`, *Documenter.jl* va générer le code html qui va constituer le site de la documentation à partir des fichiers MarkDown.

Pour commencer, on va créer un fichier index.md qui sera la page d’accueil de la documentation.


Puis, on écrit le fichier `make.jl` :

```julia
using Documenter, NewPackage

makedocs(sitename="NewPackage.jl",
    pages = ["index.md"])

```


```
$ julia make.jl
```



Avant d’héberger la documentation, on peut vérifier de quoi ça a l’air localement.
La librairie Julia ```LiveServer.jl``` va nous être utile.

```
$ julia -e 'using LiveServer; serve(dir="docs/build")'
```

### Ajout de docstring

On va maintenant ajouter de la docstring à une fonction pour illustrer comment générer la documentation automatiquement.
Il faut ajouter la macro @docs au fichier source index.md pour créer la docstring de la fonction.
Ensuite, on recréé la documentation avec make.jl.


### GitHub Workflows

- Maintenant qu’on est satisfait de notre site, on peut l’héberger sur GitHub.
- On va d’abord créer un environnement pour construire la documentation avec Pkg.
- Ensuite, on ajoute un fichier documentation.yml au dossier .github/workflows.
- https://documenter.juliadocs.org/stable/man/hosting/#GitHub-Actions
- On va aussi créer un fichier .gitignore pour ne pas inclure les fichiers créer par Documenter au suivi des modifications par git.

- Il suffit ensuite de faire un git push de la documentation sur GitHub.
- On peut ensuite vérifier que le déploiement se fait sur la branche gh-pages.
- Si tout est bien configuré, on retrouve notre documentation.
- Il ne reste qu’à ajouter les badges à notre README.

---
## 6. Enregistrement au registre de Julia

- https://github.com/JuliaRegistries/Registrator.jl

### License 

On va ajouter une licence pour compléter notre répertoire.

