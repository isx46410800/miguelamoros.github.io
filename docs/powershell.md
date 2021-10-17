# COMANDOS POWERSHELL WINDOWS  

+ [DOC](https://github.com/PowerShell/PowerShell/blob/master/docs/learning-powershell/README.md)  

+ Get-Help + COMANDO  
> Especialmente muy útil para usuarios novatos en el uso de Powershell, este comando presenta una ayuda básica para conocer más acerca de los cmdlets y sus funciones `Get-Help ls`.  

+ ls -Force directorio  
> Para listar directorios, -Force para archivos ocultos.  

+ cd directorio  
> Para cambiar de directorios  

+ mkdir directorio  
> Para crear directorio  

+ history  
> Para ver el historial de comandos  

+ Cp file dir/file1 // cp dir1 dir/dir1 -Recurse -Verbose  
> Para copiar archivos y directorios con contenido.  

+ mv file1 dir/file1  
> Para mover archivos o cambiar nombres.  

+ rm file o rm -Force file o rm dir1 - Recurse  
> Para borrar archivos o directorios.  

+ cat file1.txt // cat file1.txt -Head/Tail 10  
> Ver contenido de un archivo  

+ start notepad++ file2.txt  
> editar un archivo.  

+ Get-Alias ls  
> ver de donde proviene un comando.  

+ Select-String palabra dir/file.txt // ls dir/ -Recurse -Filter *.exe  
> Para buscar palabras dentro de contenido de archivos. Se ha de activar la opción de indexing options en windows para buscar en ellos.  

+ cat words.txt | Select-String st > st_words.txt  
> Redireccionamientos de comandos.  

+ Get-LocalUser // Get-LocalGroup // Get-LocalGroupMember namegroup  
> ir a Computer Management > Users/groups en gráfico.  

+ net user miguel "contraseña" // net user miguel * // net user miguel /logonpasswordchg:yes
> Cambiar la contraseña de un usuario. El * para escribirlo de manera oculta. /logonpasswordchg:yes para que cambie la contraseña en el proximo inicio de sesion.  

+ net user miguel */contraseña /add /logonpasswordchg:yes
> Añadir usuario y que cambie al proximo inicio de sesion.  

+ net user miguel /del // Remove-LocalUser miguel  
> Borrar un usuario.  

+ icacls dir/file  
> ver los [permisos](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732880(v=ws.11)?redirectedfrom=MSDN) de un fichero o archivo. En interfaz, se va a propiedades del fichero o carpeta y se pueden ver/editar.  

+ icacls dir/file /grant 'Everyone:(OI)(CI)(R)/Everyone:(OI)(CI)(IO)(R)'  
> Cambiamos permisos de directorio(3) y ficheros(4) en windows. Ayuda de icacls /?  

+ Compress-Archive -Path /dir/* /dir/file.zip  
> Comprimir archivos a formato [.zip](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.archive/compress-archive?view=powershell-7.1&viewFallbackFrom=powershell-5.0)  

+ Register-PackageSource -Name chocolatey -ProviderName Chocolatey -Location http://chocolatey.org/api/v2  
> Instalar repositorio para encontrar softwares y dependencias.  

+ Get-PackageSource  
> Ver las fuentes de repositorios  

+ Find-Package sysinternals -IncluseDependencies  
> Buscar un paquete  

+ Install-Package/Uninstall-Package sysinternals  
> Instalar o borrar un paquete

+ Get-Package -name sysinternals  
> Ver un paquete si está instalado o su info.  

+ tasklist / Get-Process / Get-Process | Sort CPU -descending | Select -first 3 - Property ID,RAM,CPU  
> Para ver los procesos del sistema.[DOC](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-process?view=powershell-5.1).  







































+ Get-Host  
> Con la ejecución de este comando se obtiene la versión de Windows PowerShell que está usando el sistema.  

+ Get-History  
> Con este comando se obtiene un historial de todos los comandos que se ejecutaron bajo una sesión de PowerShell y que actualmente se encuentran ejecutándose.  

+ Get-Random  
> Ejecutando este comando se obtiene un número aleatorio entre 0 y 2.147.483.646.  

+ Get-Service  
> Será necesario saber qué servicios se instalaron en el sistema, para lo que se puede usar el comando Get-Service, que brindará información acerca de los servicios que se están ejecutando y los que ya fueron detenidos.  

+ Get-Command    
> Windows PowerShell permite descubrir sus comandos y características mediante Get-Command. Muestra la lista de comandos de una función específica o para un propósito específico basado en tu parámetro de búsqueda (Get-Command *-service*)  

+ Get-Date  
> Para saber de una forma rápida qué día fue en una determinada fecha del pasado, usando este comando se obtendrá el día exacto.  

+ Copy-Item  
> Con este comando se pueden copiar carpetas o archivos (Copy-Item "C:\Proyectos.htm" -Destination "C:\MyData\Proyectos.txt".)  

+ Invoke-Command  
> En el momento en que quieras ejecutar un script o un comando PowerShell (de forma local o remota, en uno o varios ordenadores), «Invoke-Command» va a ser tu mejor opción. Es simple de utilizar y te ayudará a gestionar ordenadores por lotes.(Invoke-Command -ScriptBlock {Get-EventLog system -Newest 50} -ComputerName Server01)  

+ Invoke-WebRequest  
> A través de este cmdlet, similar a cURL en Linux, se puede hacer un inicio de sesión, un scraping y la descarga de información relacionada a servicios y páginas web, mientras se trabaja desde la interfaz de PowerShell haciendo el monitoreo de algún sitio web del que se desee obtener esta información ((Invoke-WebRequest –Uri ‘https://wwww.ebay.com’).Links)  

+ Get-Item  
> En caso de que estés buscando información acerca de un elemento con una ubicación concreta, como podría ser un directorio en el disco duro, el comando Get-Item resulta el indicado para esta tarea (Get-Item file.txt /Home/Documents)  

+ Remove-Item  
> En caso de que desees borrar elementos como carpetas, archivos, funciones y variables y claves del registro, Remove-Item será el mejor cmdlet. Lo importante es que ofrece parámetros para introducir y expulsar elementos (Remove-Item "C:\MyData\Finanzas.txt")  

+ Get-Content  
> Cuando necesites todo lo que incluye en cuanto a contenido un archivo de texto en una ruta concreta, ábrelo y léelo utilizando un editor de textos como el Bloc de Notas (Get-Content "C:\Proyectos.htm" -TotalCount 20)  

+ Set-Content  
> Con este cmdlet es posible almacenar texto en un archivo, algo parecido a lo que se puede hacer con «echo» en el Bash. Si se usa en combinación con el cmdlet Get-Content, se puede ver primero qué es lo que contiene un determinado archivo para posteriormente hacer la copia a otro archivo a través de Set-Content (Get-Content "C:\Proyectos.htm" -TotalCount 30 | Set-Content "Ejemplo.txt")  

+ Get-Variable  
> Si estás en PowerShell tratando de utilizar variables, esto podrá ser hecho con el cmdlet Get-Variable, con el que vas a poder visualizar dichos valores (Set-Variable -Name "descuento" -Value "Aquí se fija el valor")  

+ Start-Process  
> Con este cmdlet, Windows PowerShell hace que sea mucho más fácil ejecutar procesos en el equipo (Start-Process -FilePath “calc” –Verb)  

+ Start-Service  
> Si necesitas comenzar un servicio en el PC, el cmdlet Start-Service es el indicado en este caso, sirviendo de igual modo aunque dicho servicio esté deshabilitado en el PC (Start-Service -Name "WSearch").  

+ Copy-Item  
> Si necesitas copiar archivos y directorios en tu disco de almacenamiento o entradas y claves de registro, puedes usar Copy-Item ( Copy-Item "Geek.htm" -Destination "D:\BLOG\Geeks.txt")  

+ ConvertTo-HTML  
> PowerShell puede proporcionar información asombrosa sobre tu sistema. Sin embargo, lo presenta principalmente en un formato ‘indigerible’, por eso puedes usar ConvertTo-HTML para crear y formatear un informe y analizarlo o enviarlo a alguien (Get-Service | ConvertTo-HTML -Property Name, Status > C:\Users\Alex\Desk
top\Servicios.htm)  





