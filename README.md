# notes-julia

```bash
$ git --version
```

```bash
$ git config --list
```

```bash
$ git config --global user.name "Gabriel Gobeil"
$ git config --global user.email gabriel.gobeil@polymtl.ca
```

```bash
$ ssh-keygen -t ed25519 -C "gabriel.gobeil@polymtl.ca
$ eval "$(ssh-agent -s)"
$ ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

```bash
$ pbcopy < ~/.shh/id_ed25519.pub
```

```julia
julia> ]
pkg> generate NewPackage.jl
```

```bash
$ git init
$ git status
$ git add 
$ git commit -m ""
```

```bash
$ git remote add origin git@github.com:houton199/NewPackage.jl.git
$ git push -u origin master
```

```bash
$ touch README.md
$ vi README.md
```

```bash
$ git add README.md
$ git commit -m "Ajout du README"
$ git push origin master
```
