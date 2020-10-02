# Comandos para Bash Scripting
`#! /bin/bash`

## validar argumentos
```
# indica el numero de argumentos pasados
echo $#
# si no tiene 2 args, plegar
if [ $# -ne 2 ]
then
    echo "Num de args incorrecte"
    echo "USAGE: prog.sh arg1 arg2"
    exit 1
fi
# mostrar los args
echo "Los argumentos son $1 y $2"
exit 0
```

## validar help
```
#help
if [ $1 == "--help" -o $1 == "-h" ]
then
    echo "mostramos ayuda"
    exit 0
fi
```

## comparadores
+ -lt / -gt / -eq / -ne / -ge / -le _más grande, menor que..._
+ -d / -f / -h _directorio, file, link_
+ -n _mientras sea nulo_
+ $? _estatus level de error_
+ >>/dev/stderr / >>/dev/null

```
if [ $# -eq 3 ]
then
	if ! [ "$1" == "-b" -a "$2" == "-a" ]
	then
		echo "ERROR: format args incorrecte"
		echo "USAGE: prog arg -h / prog -a arg / prog -b -a arg"
		exit $ERR_NARGS
        fi
fi
```

## conficional if
```
edad=$1
echo $edad
if [ $edad -lt 18 ]
then
    echo "Es menor de edat"
elif [ $edad -eq 18 ]
then
    echo "18 recien cumplidos"
else
    echo "Ya es mayor de edad"
fi
```

## bucle for
```
count=0
for arg in $*
do
    count=$((count+1))
    echo "$count: $arg"
done
exit 0
```

## bucle while
```
cont=0
MAX=$1
while [ $cont -le $MAX ]
do
    echo $cont
    cont=$((cont+1))
done
exit 0
```

## bucle esac
```
for mes in $*
do
    case $mes in
        '2')
            echo "$mes tiene 28 dias";;
        '1'|'3'|'5'|'7'|'8'|'10'|'12')
            echo "$mes tiene 31 dias";;
        '2'|'4'|'6'|'9'|'11')
            echo "$mes tiene 30 dias";;
        *)
            echo "$mes no es un mes correcto"
    esac
done
exit 0
```

## leer por entrada standard
```
count=0
while read -r line
do
    count=$((count+1))
    echo "$count: $line"
done
exit 0
```

```
MAX=$1
cont=1
while read -r line && [ $cont -le $MAX ]
do
    echo "$cont: $line"
    cont=$((cont+1))

done
exit 0
```

## separar opciones con shift
```
opciones=''
argumentos=''
numeros=''
files=''
while [ -n "$1" ]
do
    case $1 in
        '-a') 
            files=$2
            shift;;
        '-c'|'-b'|'-e') 
            opciones="$opciones $1";;
        '-d') 
            numeros=$2
            shift;;
        *)  
            argumentos="$argumentos $1";;
    esac
    shift
done
echo "opciones: $opciones"
echo "argumentos: $argumentos"
echo "numeros: $numeros"
echo "files: $files"
exit 0
```

## mirar si es directorio, regular file...
```
#files y dir a copiar
llistafiles=$(echo $* | cut -d' ' -f1-$(($#-1)))
dirdesti=$(echo $* | cut -d' ' -f$#)
#comprobar que sea un directorio
if ! [ -d $dirdesti ]
then
    echo "error, no es un dir desti correcto"
    exit 2
fi
# para cada file validada lo copiamos al dir
for file in $llistafiles
do 
    if ! [ -f $file ]
    then
        echo "error, no es un file" >> /dev/stderr
        exit 3
    fi
    cp $file $dirdesti/.
done
exit 0
```

```
for dir in $*
do
    # miramos si es un help
    if [ $dir == "--help" -o $dir == "-h" ]
    then
        echo "mostramos ayuda"
        exit 0
    fi
    # verificar que es un dir
    if ! [ -d $dir ]
    then
        echo "error arg no es un dir"
        echo "USAGE: prog.sh arg[dir]"
        exit 2
    fi
    llistadir=$(ls $dir)
    count=1
    for file in $llistadir
    do
    #de cada cosa que imprime decir si es un regular file, dir, link o altre cosa
        if [ -d $dir/$file ]
        then
            echo "es un directorio"
            echo "$count: $file"
        elif [ -f $dir/$file ]
        then
            echo "es un file"
            echo "$count: $file"
        elif [ -d $dir/$file ]
        then
            echo "es una altra cosa"
            echo "$count: $file"
        fi
        count=$((count+1))
    done
done
exit 0
```

## Mayusculas , copiar , buscar , ordenar
`tr`  
echo "hola" | tr -s '[a-z]' '[A-Z]'  
echo "hola" | tr -s '[:blank:]' ' '  #'\t' tabulador

`cut`  
+ cut -d' ' -f1-5
> corta por campos y delimitador
+ cut -c1-50
> corta por caracteres
llistafiles=$(echo $* | cut -d' ' -f1-$(($#-1)))  
dirdesti=$(echo $* | cut -d' ' -f$#)  
uid=$(echo $loginLine | cut -d: -f3)

`grep/egrep`  
+ egrep "^[^ ]{10,}"
> busca de lo que se pase o line algo que tenga mas de 10 chars
+ echo $arg | egrep ".{4,}"
> busca de lo que se pase que tenga mas de 4 chars
+ egrep "^$user:" /etc/passwd
> buscar el campo user del /etc/passwd
+ egrep "^[^:]*:[^:]*:[^:]*:$gid:" /etc/passwd 
+ sort -u 2> /dev/null)
> ordenar unico y quita los repetidos
+ listshells=$(cut -d: -f7 /etc/passwd | sort -u 2> /dev/null)

## Funciones
function nombreFuncion(){
    return xxxxx
}

```
function showUser(){
	ERR_NARGS=1
	ERR_NOLOGIN=2
	OK=0
	#validar args
	if [ $# -ne 1 ]
	then
		echo "ERR: num args incorrecte"
		echo "USAGE: $0 login"
		return $ERR_NARGS
	fi
	#validar si existe el login
	login=$1
	userLine=$(egrep "^$login:" /etc/passwd 2> /dev/null)
	if [ $? -ne 0 ]
	then
		echo "ERR: el login $login no existe"
		return $ERR_NOLOGIN
	fi
	#mostrar
	uid=$(echo $userLine | cut -d: -f3)
	gid=$(echo $userLine | cut -d: -f4)
	gecos=$(echo $userLine | cut -d: -f5)
	home=$(echo $userLine | cut -d: -f6)
	shell=$(echo $userLine | cut -d: -f7)
	echo "uid: $uid"
	echo "gid: $gid"
	echo "gecos: $gecos"
	echo "home: $home"
	echo "shell: $shell"
	return $OK
}
```