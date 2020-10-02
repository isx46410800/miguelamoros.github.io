# Comandos para GIT
* git add .  
* git status  
* git commit -m "..."  
* git init .  
* git config --global user.email  
* git config --global --list  
* git branch -va  
* git checkout `branch`  
* git checkout -b `branch`  
* git checkout -d/-D `branch`  
* git checkout -- `file`  
* git checkout head~3  
* git merge `branch`  
* git show `hash`  
* git log `branch`   
* git diff `branch...branch`  
* git remote remove/add origin master/branch  
* git push/pull/fetch -u origin master/branch  
* git reset ~2    
* git reset HEAD~1  

## Obtener claves para GIT
* ssh-keygen  
* Copiamos la publica en repo git  
* Comprobamos con `ssh -T xxx@gitlab.com`  

## Github pages  
[Tutorial GithHub pages](https://pages.github.com/)

1. Creamos repositorio con extensión `github.io->https://github.com/isx46410800/miguelamoros.github.io`
2. Clonamos, metemos la chicha de MKdocs.
3. Hacemos un `mkdocs build` y un `mkdocs gh-deploy` y nos dará un link de nuestra web estática generada por mkdocs en Github. `https://isx46410800.github.io/miguelamoros.github.io`  

