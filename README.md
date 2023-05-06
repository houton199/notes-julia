# notes-julia

## Base

### Étape 1 : Configuration de git

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

### Étape 2 : Génération d'une librairie de base avec Pkg

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

Ce dernier fichier contient le nom de la librairie, son numéro d'identification unique, son numéro de version ainsi que les informations sur l'auteur, obtenus à partir de votre configuration git :

```
name = "NewPackage"
uuid = "11071f02-195a-4744-98d5-929847dbbb65"
authors = ["Gabriel Gobeil <gabriel.gobeil199@gmail.com>"]
version = "0.1.0"
```

### Étape 3 : Suivi des modifications avec git

#### Initialisation 

Pour activer le suivi des modifications avec git, il faut que git soit initialisé dans le répertoire de la librairie :

```bash
$ git init
```

Cela va créer un dossier caché `.git` nécessaire à git.

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



```
$ git log
```

### Étape 3 : Synchronisation des changements avec GitHub

On aimerait pouvoir envoyer nos fichiers vers GitHub pour les partager avec le monde. Avant cela, il faut configurer notre compte pour qu'il reconnaisse notre ordinateur :

#### Clé SSH

```bash
$ ssh-keygen -t ed25519 -C "gabriel.gobeil199@gmail.com"
$ eval "$(ssh-agent -s)"
$ ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

On copie la clé dans notre presse-papier afin de la recopier dans le champs approprié sur GitHub :

```bash
$ pbcopy < ~/.shh/id_ed25519.pub
```

#### Synchronisation avec le dépot à distance 

```bash
$ git remote add origin git@github.com:houton199/NewPackage.jl.git
$ git push -u origin master
```

## Développement

```bash
$ touch README.md
$ vi README.md
```

#### Commit

```bash
$ git add README.md
$ git commit -m "Ajout du README"
```

#### Synchronisation avec le dépôt à distance

```bash
$ git push origin master
```


## Tests


## Intégration continue


## Documentation
