# Intro Programación PYTHON  
## Shebang
```
# !/usr/bin/python3
# -*-coding: utf-8-*-
```

## Server Python  
`python -m SimpleHTTPDServer 80`  

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

# AVANZADO PYTHON  

+ Todos los [COMMANDOS](https://pythonbasics.org/) interesantes de python y utilidades que se integran con él.  

+ import LIBRERIA

+ from MODULO import FUNCION  

## FUNCIONES  

+ Definir una funcion:
```
## FUNCION TABLA MULTIPLICAR
def TablaMultiplicar(numero, rango):
    for i in range(rango+1):
        print(f'{numero} X {i} = {numero+i}')

TablaMultiplicar(7,2)
TablaMultiplicar(10,3)
```  
> Print se pone al llamar la funcion para que te de un return, si no se pone nada, te dará los mensajes print de dentro de la funcion.  
> Variables `global` dentro de una funcion, se puede utilizar dentro y fuera de esa funcion.  
> Si como argumento ponemos (*argumento), devuelve una tupla.  

### Funcion type  

+ para saber que tipo de valor es `type(valor)`  

### Funcion float, int  

+ Transforma el valor en un decimal o entero `float(numeroint)` 

### Funcion MATH  

+ De la liberia math(from match import sqrt,pow).
+ hace una subida exponencial `pow(5,2)==5**2`  
+ Hace la raiz cuadrada `sqrt(100)`  
+ Valor absoluto de un numero `abs(-5)`  

### Funciones strings  

+ texto.lower(), texto.upper(), texto.capitalize(), texto.title(), texto.swapcase(), texto.strip(), texto.split(' '), texto.replace(' ', 'r'), len(texto).  

### Funciones booleanos  

+ text.isupper(),texto.islower(), texto.startswith('a'), texto.endswith('z'). Si es mayusculas, minisculas, si empieza o termina en tal.  

### Funciones listas  

+ lista.remove,insert,pop,append,sort,reverse,count,index,max,min,average.  
+ lista[1:-3].  

### Funciones diccionarios  

+ dict.pop,update,get,setdefault,copy,items,values,keys.  

### Funciones conjuntos y tuplas  

+ conjunto.add, update, remove, discard, update, pop, clear.  
+ tupla[posicion]  

## GESTION ERRORES  
```
while True:
    try:
        edad = int(input("Dime tu edad: "))
        print("Tu edad es:", edad)
        break
    except ZeroDivisionError:
        print("No se puede dividir entre zero")
    except ValueError:
        print("Numero incorrecto")
    except KeyboardInterrupt:
        print("Has cancelado la ejecucion")
        break
    finally:
        print("codigo ejecutado correctamente")
```  
> Hay varios except como divisionzero, keyboardinterrupt, error value...  

## SELENIUM WEB DRIVER  

+ Es una herramienta que sirve para automatizar pruebas de testing en navegadores web.

+ No se instala de manera nativa, sino en un IDE se corre como PyCharm.  

+ Multiplataforma.  

+ No tiene soporte, solo es para navegadores web.  

### PyCharm  

+ [PyCharm](https://www.jetbrains.com/es-es/pycharm/) es este famoso IDE que además cuenta con una versión para las distribuciones Gnu/Linux, lo que hace que sea más sencillo aún su utilización y creación de programas con este lenguaje de programación.PyCharm es un IDE, es decir, no solo es un editor de código sino que también tiene un depurador, un interprete y otras herramientas que nos ayudarán a crear y exportar los programas que creemos. PyCharm tiene un interprete en el editor de código que nos ayudará a saber o conocer los posibles errores del código en tiempo real, algo que ha hecho que Python y PyCharm sean elegidos por muchos usuarios que comienzan a programar.  

+ IDE: Un entorno de desarrollo integrado​​ o entorno de desarrollo interactivo, en inglés Integrated Development Environment, es una aplicación informática que proporciona servicios integrales para facilitarle al desarrollador o programador el desarrollo de software.  

+ Instalamos pycharm desde la web indicada, extraemos el tar e inicimos desde el directorio bin con `./pycharm.sh`.  

### Selenium  

+ Para poder usar [Selenium](https://www.selenium.dev/documentation/) en pycharm tenemos que instalarlo en la [web selenium](https://www.selenium.dev/downloads/) y luego instalar la version de python. Instalamos con `pip install selenium` dentro de la terminal de pycharm.  

+ Ahora necesitamos instalar drivers de [selenium](https://www.selenium.dev/downloads/). En este caso vamos a instalar el del navegador de [google chrome](https://chromedriver.chromium.org/) pero todos se instalan del mismo modo.  

+ Una vez nos bajamos el zip de chrome, descomprimimos, copiamos el chromedriver.exe, vamos a pycharm y boton derecho a nuestro proyecto y creamos un python file de prueba. Despues de nuevo creamos un package file de nombre drivers y dentro de el, boton derecho y pegamos el driver. Ya solo tendremos que descargar los navegadores que queramos y peguemos en esta carpeta los ficheros ejecutables de drivers.  

+ Ahora hay que hacer un pequeño script para ver que todo esto funcione de pycharm con selenium en el navegador:  

## TURTLE  

+ Es un modulo de Python utilizado para enseñar programacion a través de coordenadas relativas(X,Y).  

+ El objeto a programar recibe el nombre de TORTUGA.  

### Comandos basicos:  
```
#!/usr/bin/python
# importamos la libreria turtle
import turtle

# creamos la pantalla
s = turtle.Screen()

# color de la pantalla
s.bgcolor("red")
# nombre de la pestaña
s.title("Proyecto basicos turtle")

# necesitamos el objeto, la tortuga a dibujar
t = turtle.Turtle()
# personalizamos la tortuga forma,color,tinta,etc
t.shape("turtle") # arrow, triangle, classic, circle
t.shapesize(2,2,1)
t.fillcolor("orange")
t.pencolor("white")
t.color("green","blue") # borde y relleno
t.pensize(5)

# rellenar figuras
t.begin_fill()
t.color("white","blue") # borde/tinta y relleno
t.circle(100)
t.end_fill()

# dar velocidad a la tortuga (1-10)
t.speed(1)

# dar movimientos a la tortuga
t.backward(100)
t.right(90)
t.forward(100)
t.left(90)
t.forward(100)

# dar movimiento sin pintar
t.penup()
t.forward(50)
t.pendown()
t.forward(50)

# hacer un retroceso
t.undo()

# limpiar pantalla y resetear posicion
t.clear()
t.reset()

# dejar una marca como sello y seguir
t.forward(100)
t.stamp()
t.forward(100)

# movimiento perpendiculares
t.goto(100,100)
t.goto(-100,100)
t.goto(0,0) # == t.home())

# movimientos de formas
t.circle(50) #circulo diametro
t.dot(30) #punto y diametro

# esconder y mostrar de nuevo la tortuga dibujando
t.hideturtle()
t.circle(50)
t.showturtle()
t.circle(30)

# movilizar la tortuga
t.setx(100)
t.sety(-10)

# para que se quede la pantalla todo el rato
turtle.done()

# dar movimientos a la tortuga con un cuadrado
t.forward(100)
t.right(90)
t.forward(100)
t.right(90)
t.forward(100)
t.right(90)
t.forward(100)

# cuadrado automatizado
t.color("red","blue")
for i in range(4):
    t.forward(100)
    t.right(90)

# dar movimientos con un circulo
t.color("blue","yellow")
t.circle(100)
t.circle(80)
t.circle(60)
t.circle(40)
t.circle(20)

# circulo automatizado
resultado = input("Quieres dibujar?: ")
t.color("red","blue")
if resultado == "si":
    while i<=100:
        t.circle(i)
        i+=20
else:
    print("No quieres dibujar...:(")
```








