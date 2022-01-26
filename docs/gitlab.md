# Comandos para GIT

* git add .  
* git status  
* git commit -m "..."  
* git init .  
* git config --global user.email  
* git config --global user.name "isx46410800" 
* git config --global user.email "miguel14amoros@gmail.com"  
* git config --global --list  
* git branch -va  
* git checkout `branch`  
* git checkout -b `branch`  
* git checkout -d/-D `branch`  
* git checkout -- `file`  
* git checkout head~3  
* git merge `branch`  
* git show `hash`  
* git log `branch`/hash commit   
* git diff `branch...branch`  
* git remote remove/add origin master/branch  
* git push/pull/fetch -u origin master/branch  
* git reset ~2    
* git reset HEAD~1  
* git div #gestionar conflictos de archivos  
* git tag / git tag -l / git tag -l "v1.0.0"`  
* git tag -a v1.4 -m "my version 1.4"  
* git tag v1.4-lw  
* git show v1.4  
* git push origin v1.5 / git push origin --tags  
* git tag -d v1.4  
* git push origin --delete <tagname>  
* git checkout -b version2 v2.0.0  
* git blame file  


## Obtener claves para GIT
* ssh-keygen  
* Copiamos la publica en repo git  
* Comprobamos con `ssh -T xxx@gitlab.com`  

## Github pages  
[Tutorial GithHub pages](https://pages.github.com/)

1. Creamos repositorio con extensión `github.io->https://github.com/isx46410800/miguelamoros.github.io`
2. Clonamos, metemos la chicha de MKdocs.
3. Hacemos un `mkdocs build` y un `mkdocs gh-deploy` y nos dará un link de nuestra web estática generada por mkdocs en Github. `https://isx46410800.github.io/miguelamoros.github.io`  

## GitKraken  
+ Aplicación de interfaz gráfica para gestionar Git. [Descargar GitKraken](https://www.gitkraken.com/)  

## Git Tags/Releases  
+ Sirve para poner hasta donde es de mi código las diferentes versiones.  
+ Crear tag version:  
+ Ejemplo __v.1.0.0__:  
    - Primer número es major number, cambio de número es cambio grande de versión.  
    - Segundo número es minor number, cambio no tan trascendente, un cambio de alguna función, interfaz..  
    - Tercer número es un patx, correción de bugs.  

+ Listar tags:  
`git tag / git tag -l / git tag -l "v1.0.0"`  

+ Crear tags:  
`git tag -a v1.4 -m "my version 1.4"`  
`git show v1.4`  
`git tag v1.4-lw`  # etiqueta en .git ligera  

+ Subir tag porque el git push no sube los tags:  
`git push origin v1.5`  
`git push origin --tags` #varios a la vez  

+ Borrar tags:  
`git tag -d v1.4`  
`git push origin --delete <tagname>`  

+ Cambio de ramas a esa versión:  
`git checkout -b version2 v2.0.0`  

## Gitflow  
+ Flujo de trabajo en las que se puede añadir nuevas características, funciones, releases,etc...  
+ Deben existir las dos ramas master y develop.  

+ Creamos el GitFlow:  
`git flow init`  

+ Esto creará tres ramas auxiliares por defecto:  
  - feature/  
  - release/  
  - hotfix/  

### Features  
+ Añadir una nueva característica o función, lo crea como si fuera una nueva branch, feature/nameFeature:  

```
# Crear característica
git flow feature start create-contat-form
# Confirmar los cambios que se hayan realizado
git status
git add -A
git commit -m "Create contact-form.php"
# Finalizar característica
git flow feature finish create-contat-form
```  

```
#Creación de una rama de función
-Sin las extensiones de git-flow:
git checkout develop git checkout -b feature_branch 
-Cuando se utiliza la extensión de git-flow:
git flow feature start feature_branch
#Finalizar feature
-Sin las extensiones de git-flow:
git checkout develop git merge feature_branch
-Con las extensiones de git-flow:
git flow feature finish feature_branch
```  

### Hotfix  
+ Las ramas de mantenimiento o "corrección" (hotfix) se utilizan para reparar rápidamente las publicaciones de producción. Las ramas de corrección son muy similares a las ramas de publicación y a las de función, salvo porque se basan en la maestra en vez de la de desarrollo. Es la única rama que debería bifurcarse directamente a partir de la maestra. Una vez que la solución esté completa, debería fusionarse en la maestra y la de desarrollo (o la rama de publicación actual), y la maestra debería etiquetarse con un número de versión actualizado.  

+ Tener una línea de desarrollo específica para la solución de errores permite que tu equipo aborde las incidencias sin interrumpir el resto del flujo de trabajo ni esperar al siguiente ciclo de publicación. Puedes considerar las ramas de mantenimiento como ramas de publicación ad hoc que trabajan directamente con la maestra. Una rama de corrección puede crearse utilizando los siguientes métodos:  

```
# iniciar
-Sin las extensiones de git-flow:
git checkout master git checkout -b hotfix_branch
-Cuando se utilizan las extensiones de git-flow:
git flow hotfix start hotfix_branch
# finalizar
- sin 
git checkout master git merge hotfix_branch git checkout develop git merge hotfix_branch git branch -D hotfix_branch
-con 
$ git flow hotfix finish hotfix_branch
```  


### Releases  
+ Mandar una nueva versión a producción:  

```
# Crear liberación
git flow release start 1.0.0
# Confirmar los cambios que se hayan realizado
git status
git add -A
git commit -m "Add release notes"
# Finalizar liberación
git flow release finish 1.0.0
# Subir cambios de la rama develop
git checkout develop
git push
# Subir cambios de la rama master
git checkout master
git push
```  

```
# iniciar release
-Sin las extensiones de git-flow:
git checkout develop git checkout -b release/0.1.0
-Cuando se utilizan las extensiones de git-flow:
git flow release start 0.1.0 Switched to a new branch 'release/0.1.0'
# finalizar
-Sin las extensiones de git-flow:
git checkout master git merge release/0.1.0
-con la extensión de git-flow:
git flow release finish '0.1.0'
```

+ Conclusión:  
```
En cada máquina y directorio donde tengamos el repositorio la primera vez se debe inicializar el flujo de trabajo con git flow init.
#
Una vez finalizado un release o un hotfix se deben confirmar los cambios con un git push sobre develop y master
#
Se recomienda subir las etiquetas al repositorio con git push –tags para tener un control de versiones sobre la rama de master.
```  

## Emulador CMDER  
+ [Descargar](https://cmder.net/)  

## GITLAB CI/CD  

+ [APUNTES](https://apuntes.de/gitlab-integracion-continua/#gsc.tab=0)  