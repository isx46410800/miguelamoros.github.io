# CURSO JAVA  

## INSTALACION  

+ Instalamos [JAVA](https://docs.oracle.com/en/java/javase/17/install/installation-jdk-linux-platforms.html#GUID-4A6BD592-1840-4BB4-A758-4CD49E9EE88B)  
```
# cd /opt
# wget https://download.java.net/java/GA/jdk13/5b8a42f3905b406298b72d750b6919f6/33/GPL/openjdk-13_linux-x64_bin.tar.gz
# tar -xvf openjdk-13_linux-x64_bin.tar.gz
# export JAVA_HOME=/opt/jdk-13/
# export PATH=$PATH:/opt/jdk-13/bin
También puede definirlo en el archivo de inicio del shell global / etc / environment como se muestra.
# vi /etc/environment
# export JAVA_HOME=/usr/lib/jvm/java-17-openjdk
# export PATH=$PATH:/usr/lib/jvm/java-17-openjdk/bin
# source /etc/environment
```  

+ Instalamos [Apache Netbeans](https://netbeans.apache.org/download/nb126/nb126.html):  
```
wget https://dlcdn.apache.org/netbeans/netbeans-installers/12.6/Apache-NetBeans-12.6-bin-linux-x64.sh
chmod +x Apache-NetBeans-12.6-bin-linux-x64.sh
sudo ./Apache-NetBeans-12.6-bin-linux-x64.sh
```  
> Seguimos los pasos para instalar y arrancamos con `netbeans`.    
> Podemos instalar plugins en my Netbeans - available plugins.  

+ [DOCS](https://netbeans.apache.org/help/index.html)  

+ Version oscura instalando el plugin DRACULA APACHE NETBEANS.  

## HELLOWORLD  

+ CONTROL + E borra comentarios.  
+ CONTROL + SHIFT + F ajusta bien el codigo.  
+ Creamos un proyecto nuevo de Helloworld con las caracteristicas que le digamos.  
+ En pom.xml nos indica las caracteristicas de java.  
+ En source package hacemos boton derecho y nueva clase con el mismo nombre,siempre se empieza por mayusculas.  
```java
//Mi clase en Java
public class HelloWorld {  
    public static void main(String args[]){
        System.out.println("Hola Mundo desde Java"); 
    }
}
```  
> psvm y sout + tabulador y agrega ese codigo rapido  
> Si se pone solo `print` y no `println` no lo imprime en otra linea.  

## VARIABLES  

### String  

+ Se puede poner el tipo como String, int, float, chart... o var en las nuevas versiones:  
```java
public class HelloWorld {  
    public static void main(String args[]){
        String saludar = "Saludos!";
        System.out.println("Hola Mundo desde Java"); 
        System.out.println(saludar);
        //nuevas versiones se va usando var para variables
        var despedirse = "Adios";
        System.out.println(despedirse);
        // variable entero
        var numero = 1;
        System.out.println(numero);
        System.out.println(saludar + despedirse);//se junta y numeros primero lo suma
        System.out.println(saludar + " " + despedirse);
        // tipo input
        Scanner scanner = new Scanner(System.in);
        System.out.println("Proporciona un usuario: ");
        var usuario = scanner.nextLine();
        System.out.println("Usuario = " + usuario);
    }
}
```  

### Enteros  

+ Short,int,long,byte:  
```java
import java.util.Scanner;

/*
Esto es un comentario
*/
//Mi clase en Java
public class HelloWorld {  
    public static void main(String args[]){
    //byte, short, int, long
        byte byteVar = 127;
        System.out.println("byteVar = " + byteVar);

        System.out.println("bits tipo byte:" + Byte.SIZE);
        System.out.println("bytes tipos byte:" + Byte.BYTES);
        System.out.println("valor minimo tipo byte:" + Byte.MIN_VALUE);
        System.out.println("valor maximo tipo byte:" + Byte.MAX_VALUE);

        short shortVar = 32767;
        System.out.println("shortVar = " + shortVar);

        System.out.println("bits tipo short:" + Short.SIZE);
        System.out.println("bytes tipos short:" + Short.BYTES);
        System.out.println("valor minimo tipo short:" + Short.MIN_VALUE);
        System.out.println("valor maximo tipo short:" + Short.MAX_VALUE);

        int intVar = 2147483647;
        System.out.println("intVar = " + intVar);

        System.out.println("bits tipo int:" + Integer.SIZE);
        System.out.println("bytes tipos int:" + Integer.BYTES);
        System.out.println("valor minimo tipo int:" + Integer.MIN_VALUE);
        System.out.println("valor maximo tipo int:" + Integer.MAX_VALUE);

        long longVar = 9223372036854775807L;
        System.out.println("longVar = " + longVar);
        System.out.println("bits tipo long:" + Long.SIZE);
        System.out.println("bytes tipos long:" + Long.BYTES);
        System.out.println("valor minimo tipo long:" + Long.MIN_VALUE);
        System.out.println("valor maximo tipo long:" + Long.MAX_VALUE);
        
        var numeroInt = 2147483647;
        System.out.println("numeroInt = " + numeroInt);
        
        var numeroLong = 2147483648L;
        System.out.println("numeroLong = " + numeroLong);
    }
}
```  

### Flotantes  

```java
import java.util.Scanner;

public class HelloWorld {

    public static void main(String args[]) {

        var floatVar = 1000.10F;
        System.out.println("floatVar = " + floatVar);
        
        System.out.println("bits tipo float:" + Float.SIZE);
        System.out.println("bytes tipo float:" + Float.BYTES);
        System.out.println("valor minimo tipo float:" + Float.MIN_VALUE);
        System.out.println("valor maximo tipo float:" + Float.MAX_VALUE);
        
        var doubleVar = 100D;
        System.out.println("doubleVar = " + doubleVar);
        
        System.out.println("bits tipo double:" + Double.SIZE);
        System.out.println("bytes tipo double:" + Double.BYTES);
        System.out.println("valor minimo tipo double:" + Double.MIN_VALUE);
        System.out.println("valor maximo tipo double:" + Double.MAX_VALUE);
        
    }
}
```  

### Tipo chart  

+ Solo soporta un caracter.  
```java
import java.util.Scanner;

public class HelloWorld {

    public static void main(String args[]) {

        System.out.println("bits tipo char:" + Character.SIZE);
        System.out.println("bytes tipo char:" + Character.BYTES);
        System.out.println("valor minimo tipo char:" + Character.MIN_VALUE);
        System.out.println("valor maximo tipo char:" + Character.MAX_VALUE);
        
        var varChar = '\u0021';
        System.out.println("varChar = " + varChar);
        
        var varCharDecimal = 33;
        System.out.println("varCharDecimal = " + varCharDecimal);
        
        var varCharSimbolo = '!';
        System.out.println("varCharSimbolo = " + varCharSimbolo);
        
    }
}
```  


### Boleanos  

+ True o False:  
```java
import java.util.Scanner;

public class HelloWorld {

    public static void main(String args[]) {

        //boolean
        System.out.println("true tipo boolean: " + Boolean.TRUE);
        System.out.println("false tipo boolean: " + Boolean.FALSE);
        
        boolean booleanVar = false;
        
        if(booleanVar){
            System.out.println("el valor es verdadero");
        }
        else{
            System.out.println("el valor es falso");
        }
        
        System.out.println("");
        
        var edad = 30;
        var esAdulto = edad >= 18;
        System.out.println("esAdulto = " + esAdulto);
    }
}
```  

### Conversion de variables  

```java
import java.util.Scanner;

public class HelloWorld {

    public static void main(String args[]) {

        //convertir un String a un tipo int 
        var edad = Integer.parseInt("20");
        System.out.println("edad = " + edad);
        
        double valorPI = Double.parseDouble("3.1416");
        System.out.println("valorPI = " + valorPI);
        
        char c = "hola".charAt(3);
        System.out.println("c = " + c);
        //introducir una edad
        var scanner = new Scanner(System.in);
        edad = Integer.parseInt(scanner.nextLine()) ;
        System.out.println("edad = " + edad);
        
        char caracter = scanner.nextLine().charAt(0);
        System.out.println("caracter = " + caracter);
        
        String edadTexto = String.valueOf(false);
        System.out.println("edadTexto = " + edadTexto);
        
        short s = 129;
        byte b = (byte) s;
        System.out.println("b = " + b);
    }
}
```  

## OPERACIONES  

+ Aritmeticos:  
```java
import java.util.Scanner;

public class HelloWorld {

    public static void main(String args[]) {

        int a = 3, b = 2;

        var resultado = a + b;
        System.out.println("resultado suma = " + resultado);

        System.out.println("resultado suma=" + (a  + b) );
        
        resultado = a - b;
        System.out.println("resultado resta = " + resultado);
        
        System.out.println("resultado resta = " + (a - b));
        
        resultado = a * b;
        System.out.println("resultado multiplicacion = " + resultado);
        
        double resultado2 = 3D / b;
        System.out.println("resultado division = " + resultado2);
        
        resultado = a % b;
        System.out.println("resultado modulo= " + resultado);
        
        resultado = a % 2;
        System.out.println("resultado = " + resultado);
        
        resultado = 123 % 2;
        System.out.println("resultado = " + resultado);
        
        if(resultado == 0)
            System.out.println("es numero par");
        else
            System.out.println("es numero impar");
    }
}
```  

+ De asignacion:  
```java
import java.util.Scanner;

public class HelloWorld {

    public static void main(String args[]) {

        int a = 3, b = 2;

        int c = a + 5 - b;
        System.out.println("c = " + c);

        a += 1;//a=a+1
        System.out.println("a = " + a);
        
        a += 3;//a=a+3
        System.out.println("a = " + a);
        
        b -= 1;//b=b-1
        System.out.println("b = " + b);
        
        // *=, /=, %= 
    }
}
```  

+ Unarios:  
```java
import java.util.Scanner;

public class HelloWorld {

    public static void main(String args[]) {
        // el negativo para invertir
        int a = 3;
        int b = -a;
        System.out.println("b = " + b);
        // el ! para invertir el booleano
        boolean c = true;
        boolean d = !c;
        System.out.println("d = " + d);
        
        //incremento
        //preincremento
        int e = 3;
        int f = ++e; //la e se incrementa 1 y f se queda con este valor
        System.out.println("e = " + e);
        System.out.println("f = " + f);
        
        //postincrement
        int g = 5;
        int h = g++;//la g se incrementa 1 y f se queda sin incremento
        System.out.println("g = " + g);
        System.out.println("h = " + h);
        
        //decremento
        //predecremento
        int i=2;
        int j = --i;//la i se disminuye 1 y f se queda con este valor
        System.out.println("i = " + i);
        System.out.println("j = " + j);
        
        //postdecremento
        int k=4;
        int l= k--;//la k se disminuye 1 y f se queda sin restar
        System.out.println("k = " + k);
        System.out.println("l = " + l);
    }
}
```  

+ De comparacion:  
```java

import java.util.Scanner;

public class HelloWorld {

    public static void main(String args[]) {
        int a = 3, b = 4;

        boolean c = (a == b);
        System.out.println("c = " + c);

        c = (a != b);
        System.out.println("c = " + c);

        String cadena = "hola";
        String cadena2 = "hola";
        // printa comparando las dos cadenas
        System.out.println(cadena.equals(cadena2));

        boolean d = a <= b;
        System.out.println("d = " + d);

        if (b % 2 == 0) {
            System.out.println("numero par");
        } else {
            System.out.println("numero impar");
        }

        int edad = 31;
        int adulto = 18;
        if (edad >= adulto)
            System.out.println("es un adulto");
        else
            System.out.println("es menor de edad");
    }
}
```  

+ Booleanos:  
```java

import java.util.Scanner;

public class HelloWorld {

    public static void main(String args[]) {
        int a = 15;
        int valorMinimo = 0, valorMaximo=10;
        // && es un AND de si y si
        boolean resultado = a >= valorMinimo && a <= valorMaximo;
        System.out.println("resultado = " + resultado);
        
        boolean vacaciones = false;
        boolean diaDescanso = true;
        // || es un OR es de si o si
        if(vacaciones || diaDescanso)
            System.out.println("Padre puede asistir al juego del hijo");
        else
            System.out.println("Padre ocupado");
    }
}
```  

+ Ternario:  
```java

import java.util.Scanner;

public class HelloWorld {

    public static void main(String args[]) {
        // ternacio es una condicion
        // (expresion) ? true(si es verdadera) :false(o falsa)
        var resultado = (3 > 2) ? "verdadero" : false;
        System.out.println("resultado = " + resultado);
        // o la expresion puede dar un resultado entre dos
        var numero = 8;
        var par = (numero % 2 == 0) ? "numero par" : "numero impar";
        System.out.println("par = " + par);
    }
}
```  

+ Prioridad de operaciones:  
```java

import java.util.Scanner;

public class HelloWorld {

    public static void main(String args[]) {
        var x = 5;
        var y = 10;
        var z = ++x + y--;//x=6, y=9, z=(6+10)16
        System.out.println("x = " + x);
        System.out.println("y = " + y);
        System.out.println("z = " + z);

        System.out.println("\nEjemplo 2 precedencia operadores");
        var resultado = 4 + 5 * 6 / 3;// 4+((5*6)/3) => 4+(30/3) => 4+10 => 14
        System.out.println("resultado = " + resultado);

        resultado = (4 + 5) * 6 / 3; //18
        System.out.println("resultado = " + resultado);
    }
}
```  

## CONDICIONALES  

+ Cuando es una linea de sentencia se pueden ahorrar las llaves:  

+ COndicion basica:  
```java
import java.util.Scanner;
public class HelloWorld {
    public static void main(String args[]) {
        var condition = true;
        if (condition) {
            System.out.println("Es verdadera");
        } else {
            System.out.println("Es falsa");
        }
        
        var numero = 12;
        var texto = "Numero desconocido"
        if (numero == 1) {
            texto = "Numero 1";
            //System.out.println("Numero igual a 1");
        } else if (numero == 2) {
            texto = "Numero 2";
            //System.out.println("No es igual a 2");
        } else {
            texto = "Numero desconocido";
            //System.out.println("No es igual a 1 ni 2");
        }
        System.out.println(texto);
    }
}
```  

+ Ejemplo con mas condiciones y entrada input:  
```java
import java.util.Scanner;
public class HelloWorld {
    public static void main(String args[]) {
        var scanner = new Scanner(System.in);
        System.out.println("Proporciona el mes del año:");
        var mes = scanner.nextInt();//mes del año

        String estacion = null;

        if (mes == 1 || mes == 2 || mes == 12) {
            estacion = "Invierno";
        } else if (mes == 3 || mes == 4 || mes == 5) {
            estacion = "Primavera";
        } else if (mes == 6 || mes == 7 || mes == 8) {
            estacion = "Verano";
        } else if (mes == 9 || mes == 10 || mes == 11) {
            estacion = "Otoño";
        } else {
            estacion = "Mes incorrecto";
        }
        System.out.println("estacion:" + estacion + " para el mes: " + mes);
    }
}
```  

+ ESTRUCTURA SWITCH para diferentes opciones:  
```java
import java.util.Scanner;
public class HelloWorld {
    public static void main(String args[]) {
        var numero = 1;
        var numeroTexto = "numero desconocido";

        switch (numero) {
            case 1:
                numeroTexto = "numero uno";
                break;
            case 2:
                numeroTexto = "numero dos";
                break;
            case 3:
                numeroTexto = "numero tres";
                break;
            default:
                numeroTexto = "numero desconocido";
        }
        System.out.println("numero texto:" + numeroTexto + " para el numero proporcionado:" + numero);
    }
}
```  

```java
import java.util.Scanner;
public class HelloWorld {

    public static void main(String args[]) {
        var scanner = new Scanner(System.in);
        System.out.println("Proporcionar el valor del mes");
        var mes = scanner.nextInt();
        String estacion = null;
        
        switch ( mes ){
            case 1: case 2: case 12:
                estacion = "Invierno";
                break;
            case 3: case 4: case 5:
                estacion = "Primavera";
                break;
            case 6: case 7: case 8:
                estacion = "Verano";
                break;
            case 9: case 10: case 11:
                estacion = "Otoño";
                break;
            default:
                estacion = "Mes incorrecto";
        }
        
        System.out.println("estacion: " + estacion + " para el mes:" + mes);
    }
}
```  
> Para varios case a la vez.  


## BUCLES  

+ BUCLE WHILE / DO WHILE:  
```java
import java.util.Scanner;
public class HelloWorld {
    public static void main(String args[]) {
        var contador = 0;
        while (contador < 3) {
            System.out.println("contador = " + contador);
            contador++; //PARA QUE INCREMENTE 1
        }
        do {
            System.out.println("contador = " + contador);
            contador++;
        } while (contador < 3);
    }
}
```  

+ BUCLE FOR:  
```java
import java.util.Scanner;
public class HelloWorld {
    public static void main(String args[]) {
        for (var i = 0; i < 3; i++) {
            System.out.println("i = " + i);
        }
    }
}
```  
> Iniciar, condicion, incremento.  

+ BUCLES CON BREAK/CONTINUE:  
```java
import java.util.Scanner;
public class HelloWorld {
    public static void main(String args[]) {
        for (var i = 0; i < 3; i++) {
           //Imprimimos solo numeros pares
           if (i % 2 == 0) {
               System.out.println("i = " + i);
               break;
           }
       }

        for (var i = 0; i < 3; i++) {
            //Imprimimos solo numeros pares
            if (i % 2 != 0) {
                continue;
            }
            System.out.println("i = " + i);
        }
    }
}
```  

+ LABELS en los bucles:  
```java
import java.util.Scanner;
public class HelloWorld {
    public static void main(String args[]) {
        Inicio:
        for (var i = 0; i < 3; i++) {
           //Imprimimos solo numeros pares
           if (i % 2 == 0) {
               System.out.println("i = " + i);
               break Inicio;
           }
       }
    }
}
```  
> Se le pone una etiqueta antes del bucle y para hacer break/continue se llama al label.  


## CLASES JAVA  

+ Una clase es una plantilla que sirven para crear objetos. Tienen nombre, atributos y metodos.  

+ Una clase debe terminar en .java y empieza en mayusculas.  

+ Creamos un nuevo proyecto - nombre - maven - java app.  
+ Una vez creado vamos a source package - new java class y ponemos un nombre de la clase, ejemplo "Persona".  

+ El nombre de la clase a rellenar tiene que ser mismo nombre que al crear java class.  

+ Ejemplo:  
```java
public class Persona {
    //atributos de nuesta clase
    string nombre;
    string apellido;
    
    //metodos de la clase
    //lo usuaran los objetos de esta clase
    public void desplegarNombre(){
    // se pone void porque no regresa ningun dato, sino seria int,etc
        System.out.println("nombre: ", nombre);
        System.out.println("apellido; ", apellido);
    }
}
```  

## OBJETOS  

+ Cuando creamos un objeto o una clase, se reserva en memoria espacio para futuros objetos con sus atributos.  

+ la clase string para crear variables y objetos puede ser:  
`String saludo = "Hola mundo"`  
`saludo = new String("Hola Mundo")`  

+ Creamos una nueva clase java class en source packages de "PruebaPersonas".  

+ Ejemplo:  
```java
public class PruebaPersonas {
    public static void main(String[] args) {
        // creacion de un objeto de tipo persona

        // definimos la variable de tipo Persona en ref class Persona
        Persona persona1;

        // creando un objeto de la clase Persona
        persona1 = new Persona();

        // accedemos al objeto y llamamos a los metodos y atributos de clase Persona
        persona1.desplegarNombre();
        
        // asignamos valores de los atributos del objeto Persona
        persona1.nombre = "Miguel";
        persona1.apellido = "Amoros";
        persona1.desplegarNombre();
        
        // creamos otro objeto
        Persona persona2 = new Persona();
        persona2.nombre = "Natalia";
        persona2.apellido = "Sendra";
        persona2.desplegarNombre();
    }
}
```  

## METODOS  

+ Son funciones dentro de una clase.  
+ Creamos un nuevo proyecto "ProyectoAritmetica" y creamos una nueva clase "Aritmetica".   
```java
public class Aritmetica {
    // si ponemos argumentos, no son las variables de los atributos
    public int sumar(int a, int b){
        //cuerpo de creacion de nuestro metodo
        int resultado = a + b;
        return resultado; //el valor debe ser mismo tipo que sintaxi de public    
    }
}
```  

+ Despues creamos otra java class "PruebaAritmetica para llamar a nuestro metodo creado a través de la creacion de un objeto con la clase Aritmetica:  
```java
public class PruebaAritmetica {
    public static void main(String[] args) {
        // creamos un objeto de tipo Aritmetica
        Aritmetica mates = new Aritmetica();
        
        //llamamos al metodo para nuestra variable del objeto
        //mismo tipo guardado que creacion de metodo "int" ejemplo
        int resultado = mates.sumar(5, 3);
        System.out.println("El resultado es: "+ resultado);
    }
}
```  

## CONSTRUCTORES  

+ Son como los metodos pero ya se inician a la creacion del objeto.  

+ Tiene que tener el mismo nombre que la clase.  

+ Los constructores vacíos se agregan solos al compilar si no son llamados.  

+ Los constructores solo son llamados cuando creamos el objeto, no se pueden llamar como los metodos:  
```java
public class Aritmetica {
    //definimos un constructor vacio y se llama como la clase
    public Aritmetica(){
        System.out.println("Ejecutando constructor vacio");
    }
    // si ponemos argumentos, no son las variables de los atributos
    public int sumar(int a, int b){
        //cuerpo de creacion de nuestro metodo
        int resultado = a + b;
        return resultado; //el valor debe ser mismo tipo que sintaxi de public    
    }
}
```  

```java
public class PruebaAritmetica {
    public static void main(String[] args) {
        // creamos un objeto de tipo Aritmetica
        Aritmetica mates = new Aritmetica();
        
        //llamamos al metodo para nuestra variable del objeto
        //mismo tipo guardado que creacion de metodo "int" ejemplo
        int resultado = mates.sumar(5, 3);
        System.out.println("El resultado es: "+ resultado);
    }
}
```  
> Ejecutando constructor vacio  
> El resultado es: 8  

+ Constructor con argumentos, ya no agrega el vacio, solo el de argumentos:  
```java
public class Aritmetica {
    // atributos de la clase. Por defecto es 0
    int a;
    int b;
    
    //definimos un constructor vacio y se llama como la clase
    public Aritmetica(){
        //a = 2;
        //b = 5; //llamariamos al metodo de suma sin argumentos
        System.out.println("Ejecutando constructor vacio");
    }
    
    //definimos un constructor de tipo publico con argumentos
    public Aritmetica(int arg1, int arg2){
        a = arg1;
        b = arg2;
        System.out.println("Ejecutando constuctor con argumentos");
    }
    
    // creacion de un metodo
    // si ponemos argumentos, no son las variables de los atributos
    public int sumar(){
        //cuerpo de creacion de nuestro metodo
        int resultado = a + b;
        return resultado; //el valor debe ser mismo tipo que sintaxi de public    
    }
}
```  

```java
public class PruebaAritmetica {
    public static void main(String[] args) {
        // creamos un objeto de tipo Aritmetica
        Aritmetica mates = new Aritmetica();
        
        //podemos dar valores accediendo a atributos de nuestra clase
        mates.a = 3;
        mates.b = 5;
        //llamamos al metodo para nuestra variable del objeto
        //mismo tipo guardado que creacion de metodo "int" ejemplo
        int resultado = mates.sumar();
        System.out.println("El resultado es: "+ resultado);
        
        // creacion de otro objeto de tipo Aritmetica
        Aritmetica mates2 = new Aritmetica(4, 3);
        System.out.println("El resultado es: "+ mates2.sumar());
    }
}
```  
> Ejecutando constructor vacio
> El resultado es: 8
> Ejecutando constuctor con argumentos
> El resultado es: 7

