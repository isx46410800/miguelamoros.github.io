# Programación PYTHON
## Shebang
```
# !/usr/bin/python3
# -*-coding: utf-8-*-
```
## Mostrar algo
```
print("Uso windows")
```

## Mostrar algo con formato
```
print("Hello World, my name is {}" .format(name))
print(f'My Python version is {version}')
```

## Importar librerias
```
import platform
from xxxx import xx
```

## Variables
```
x=100
y=True
z="Miguel"
```

## Condicional
```
x = 25
y = 15
if x > y:
    print("x is greater than y: where x is {} and y {}" .format(x,y))
elif x == y:
    print("x and y are the same: where x is {} and y {}" .format(x,y))
else:
    print("x is lesser than y: where x is {} and y {}" .format(x,y))
```

```
tengoHambre = True
y = "Necesito comer" if tengoHambre else "solo necesito beber"
```

```
a = True
b = False
# comparamos a y b
if a and b:
    print('Expresiones son TRUE')
else:
    print("Expresiones son False")
```

## Bucle for (iterar elementos)
```
food = ["breakfast", "lunch", "snack", "dinner"]
for i in food:
    print(i)
```

## Bucle while
```
food = ["breakfast", "lunch", "snack", "dinner"]
while m < 4:
    print(food[m])
    m +=1
```
## Funciones
```
def message():
    print("Mi version de python es la {}" .format(platform.python_version()))
message()
def operation(n=25):
    print(n)
    return n*2
```

## Test main
```
if __name__ == "__main__":
    runMe()
    name("Miguel")
    name(2)
    print(x)
```

## Objetos
```
class Time:
    h = "horas"
    m = "minutos"
    s = "segundos"
    def hours(self):
        print(self.h)

    def minuts(self):
        print(self.m)

    def seconds(self):
        print(self.s)
def main2():
    how_time = Time()
    how_time.hours()
    how_time.minuts()
    how_time.seconds()
main2()
```

## Mayusculas, minusculas, letra capital
```
mayus = "hello world".upper()
minus = "hello world".lower()
capi = "hello world".capitalize()
```

## Ver tipo de dato
```
print(type(w)) #float
```

## Listas
```
x = [1,2,3,4]
print(x[2])
x[2] = 10
for i in x:
    print(i)
```

```
# LISTA ACCIONES -- TUPLAS SON INMUTABLES Y NO SE PUEDE
def main():
    lista = ['perro', 'gato', 'cerdo', 'caballo']
    lista2 = ['perro', 'gato', 'cerdo', 'caballo']
    print(lista[1]) # gato
    print(lista[1:3]) # gato, cerdo
    print(lista[0:5:2]) # perro, cerdo
    print(lista.index('gato')) # 1, busca la posicion de esa palabra
    lista.append('koala') # añade koala
    lista.insert(0, 'vaca') # añade vaca en posicion 0
    lista.remove("vaca") # borra de la lista vaca
    lista.pop() # borra el ultimo elemento de la lista
    lista.pop(1) # borra esa posicion de la lista
    del lista[1] # borra de la lista ese elemento
    del lista[0:1] # borra ese slicing
    print(len(lista)) # cuenta en numero de elementos de la lista
    lista.extend(lista2) # junta dos listas
    print_lista(lista) # funcion de iterar la lista
# funcion para iterar la lista pasada por argumento
def print_lista(lista):
    for i in lista:
        print(i, end=' ', flush=True)
    print()
```

## Tuplas
```
t = (1,2,3,4,5)
# cosas con tuplas
## t[2] = 10 NO SE PUEDE ASIGNAR PARA CAMBIAR
print(t[2])
for e in t:
    print(e)
```

## Diccionarios
```
dic = { 'x' : 5, 'y' : 'miguel', 'z' : False }
# cosas con diccionarios
print(dic['y'])
dic['y'] = 'miguelito'
for id, valor in dic.items():
    print(f"id: {id} valor: {valor}")
for e in dic.values():
    print(e)
for e in dic:
    print(f'el id es {e}')
    print(f'el valor es {dic[e]}')
```

```
gente = {'1': "miguel", '2': "cristina", '3': "isabel"}
gente['4'] = 'maria'
for k in gente:
    print(k)
for k,v in gente.items():
    print(f'key: {k}   valor: {v}')
for k in gente.keys():
    print(f'key: {k}')
for v in gente.values():
    print(f'valor: {v}')
```

## Rangos
```
r = range(5) # no se puede asignar sino es con una lista
ra = list(range(5))
ra[2] = 20
rang = range(5,10,2) # del 5 al 10 de dos en dos
# cosas con rangos
for e in ra:
    print(e)
for e in rang:
    print(e)
```

## List Comprension
```
# de una lista
lista = range(11)
tupla = ((0,1),(1,2),(2,3))
# creas una lista,tupla iterando lista y operaciones
lista2 = [ x * 2 for x in lista]
tupla2 = [ (y*2, x*2) for x,y in tupla]
# resultados
print(lista2)
print(tupla2)
```

## Len
```
len(*args/lista)
```

## Objetos
```
# definimos una clase
class mobile:
    #definimos unas variables con contenido
    old_phone = "keypad"
    new_phone = "touch screen"

    # definimos funciones que printes esas variables
    def old_mobile(self):
        print(self.old_phone)
    
    def new_mobile(self):
        print(self.new_phone)
# creamos funcion,variable con objeto y sus dos partes de funciones
def main():
    x = mobile()
    x.old_mobile()
    x.new_mobile()
```

```
class Animal:
    def __init__(self, type, name, sound):
        self._type = type
        self._name = name
        self._sound = sound

    def type(self):
        return self._type

    def name(self):
        return self._name

    def sound(self):
        return self._sound
def print_animal(x):
    if not isinstance(x, Animal):
        raise TypeError("error, requiere un animal")
    print(f'El {x.type()} se llama {x.name()} y dice {x.sound()}')
# le pasamos a la funcion de hacer algo, los argumentos al objeto
def main():
    print_animal(Animal("Kitten", "Fluffly", "Meow"))
    print_animal(Animal("Duck", "Donald", "Quak"))
```

## Ficheros
+ Leer
```
def main():
    file = open('lines.txt', 'r')
    # file = open('lines.txt', 'r') # read only
    # file = open('lines.txt', 'w') # write only (empties files)
    # file = open('lines.txt', 'a') # añadir data in files
    # file = open('lines.txt', 'r+') # optional + read or write
    for line in file:
        print(line.rstrip()) #rstrip elimina espacios o lo que se ponga en ()
```

+ Escribir
```
def main():
    fileInput = open('lines.txt', 'rt') # r read t text
    fileOutput = open('linesOutput.txt', 'wt') # w write t text
    for line in fileInput:
        print(line.rstrip(), file=fileOutput) # cada linea sin blancos la envia al nuevo file
        print('.', end='', flush=True) # aqui solo printa esto por cada linea leida
    fileOutput.close() # cierra el doc nuevo
    print('\nDone.') # printa que se ha realizado todo
```

+ Copiar
```
def main():
    fileInput = open('cat.jpg', 'rb') # r read b binario
    fileOutput = open('cat_copy.jpg', 'wb') # w write b binario
    # mientras todo se pueda
    while True:
        # leemos datos y lo metemos en un buffer
        buffer = fileInput.read(102400)
        # mientras haya buffer por leer
        if buffer:
            # copiamos del buffer en el file nuevo
            fileOutput.write(buffer)
            print('.', end='', flush=True) # aqui solo printa esto por cada linea leida
        else:
            break
    fileOutput.close() # cierra el doc nuevo
    print('\nDone.') # printa que se ha realizado todo
```

## Módulos
```
import os, datetime, sys
def main():
    # modulo de system
    v = sys.version_info
    print('Mi version es {}.{}.{}' .format(*v))
    # modulo de operating system
    x = os.name
    w = os.getcwdb()
    print(v)
    print(w)
    # modulo de datetime
    date = datetime.datetime.now() # fecha y hora de ahora
    print(date)
    print(date.year)
    print(date.month)
    print(date.day)
```