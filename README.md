[English version](README_EN.md)

# Práctica de DNS

### Introducción a Dig
Para realizar esta práctica usaremos el programa dig. A continuación resumimos las principales opciones de este comando, pero le recomendamos que consulte la página de manual completa haciendo `man dig`.

La llamada a dig con sus principales opciones son las siguientes (`[]` indica opcional):
```
dig [@server] [-p port#] [name] [type] [queryopt...] 
```
* `@server`: nombre o dirección IP del servidor DNS al que se hace la consulta. Si no se especifica, se consulta a los servidores que se listan en el fichero /etc/resolv.conf.
* `-p port`: sirve para indicar un puerto de servidor, distinto del puerto por defecto (53).
* `name`: nombre del registro de recurso que se desea consultar.
* `type`: tipo del registro de recurso que se desea consultar: ANY, A, MX, etc. Si no se especifica ninguno, por defecto es el tipo A.
* `queryopt`: una o más de las siguientes opciones:
..* `+tcp`: para forzar que la consulta se haga por TCP (por defecto UDP, excepto AXFR o IXFR). `+notcp` para forzar UDP
..* `+ignore`: para no reintentar consulta por TCP si la respuesta UDP está truncada.
..* `+norecurse`: para forzar consulta iterativa (por defecto es recursiva).


La práctica está dividida en dos partes: 
* en la primera parte utilizaremos el programa dig para realizar consultas DNS, 
* en la segunda parte configuraremos y ejecutaremos nuestro propio servidor DNS.

## Parte 1: Consultas con un cliente DNS
Usando el programa dig responda a las siguientes preguntas y diga cómo ha obtenido la respuesta:
### 1. Determine la dirección IP de la máquina `www.mec.es`.
### 2. Averigüe qué máquina tiene asignada la dirección IP `193.110.128.199`.
### 3. Con relación al dominio abc.es, averigüe el nombre y dirección IP de los servidores de DNS de dicho dominio, y diga cuál es el primario y cuáles los secundarios.
### 4. Obtenga el registro SOA del dominio `abc.es` preguntándoselo al servidor DNS del laboratorio en la que está haciendo la práctica, y preguntándoselo directamente al servidor primario del dominio `abc.es`. Compruebe que en un caso es información autorizada y en otro no.
### 5. Si tuviera un problema con el DNS de `abc.es` y tuviera que mandar un correo electrónico a su administrador, ¿A qué dirección de e-mail lo enviaría?
### 6. Determine el nombre y dirección IP del servidor de correo del administrador al que se refiere la pregunta anterior.
### 7. ¿Cuánto tiempo almacenará en caché (TTL original) la dirección IP de `www.vanguardia.es`? Pregunte varias veces al DNS por esta dirección. ¿Qué observa en el TTL del registro de recurso?
### 8. Pregunte ahora lo mismo a un servidor raíz (por ejemplo, `J.ROOT-SERVERS.NET` con dirección IP `192.58.128.30`) y compruebe en el paquete de respuesta que, dicho servidor no acepta el modo recursivo.
### 9. Averigüe cuántas máquinas están realizando balanceo de carga en el servidor web `www.elpais.es`. Pregunte varias veces al DNS. ¿Obtiene siempre las mismas y en el mismo orden?
### 10. Haciendo consultas iterativas, averigüe la dirección IP de `www.pcreview.co.uk` ¿Qué pasos ha dado? 

> Nota: si el servidor DNS tiene el registro en la cache, es posible que no responda con el siguiente paso y devuelva directamente el resultado. En ese caso, comienza preguntando directamente a un servidor raíz, por ejemplo, `dig +norecurse www.pcreview.co.uk @A.ROOT-SERVERS.NET`, y continua.

### Siguiendo los mismos pasos (consultas iterativas) ¿Se obtiene la dirección IP de `www.bbc.co.uk`?

### 11. Puede hacer esto mismo (punto anterior) con la opción `+trace` de dig. Compruebe el resultado que obtiene.
### 12. Utilizando la información disponible a través del DNS determine (nombre y dirección IP) la máquina o máquinas que actúan como servidoras de correo del dominio `gmail.com`.
### 13. ¿Cómo haría para obtener todos los registros de recurso de la zona `lab.it.uc3m.es`?

### 14. Averigua cuáles de los siguientes nombres de dominio son una zona: `google.jobs`, `primevideo.com`, `inf.uc3m.es`, `it.uc3m.es`.

### 15. Identifica los servidores DNS, servidores de correo, la dirección del administrador del dominio, e identifica los tiempos de copia secundario-primario, tiempo de expiración, así como TTL mínimo de los dominios `it.uc3m.es` y `csic.es`. 


## Parte 2:  Creación de un dominio en un servidor DNS named

### Configuración del servidor DNS named

`named` es el servidor DNS que forma parte de la implementación más extendida de DNS en Unix, conocida como BIND (Berkeley Internet Name Domain). Nosotros vamos a utilizar la versión de named que se encuentra en el directorio `/usr/dist/sbin/` de las máquinas de los laboratorios

Al arrancar, named lee un fichero de configuración que por defecto está en el directorio `/etc/bind/named.conf`, pero podemos especificarle otro fichero distinto por línea de comando lo que es conveniente para esta práctica dado que no tenemos permisos para editar la configuración por defecto. Para usar un fichero distinto se utiliza la opción `-f` (ver página de manual de named). 
Por tanto, crearemos nuestro propio fichero de configuración named.conf (con el contenido especificado más adelante) y arrancaremos el servidor usando el siguiente comando **desde la carpeta en la que se encuentre nuestro fichero**:

```
/usr/dist/sbin/named -c ./named.conf -f
```

Este fichero named.conf se divide en dos partes:
* En el apartado de opciones (options) se configuran cosas como el puerto en el que va a escuchar (al arrancarlo en modo usuario no podrá ser el 53, necesitaremos especificar un puerto no reservado como por ejemplo el 10053) y la localización de ciertos ficheros que va a tener que escribir durante su funcionamiento (que pondremos para que estén en nuestra cuenta).
* En el apartado de zonas (zone) se define una o más zonas DNS que sirve este servidor.

Un **ejemplo** de fichero de configuración sería el siguiente (estamos **suponiendo que hace la práctica en un subdirectorio rroo/named de su cuenta**; sustituya $HOME por el camino completo de su HOME). Que puede averiguarlo usando `echo $HOME`:

```
# file $HOME/rroo/named/named.conf
options {
 port 10053;
directory "$HOME/rroo/named/";
 pid-file "$HOME/rroo/named/named.pid";
};
zone "0.0.127.in-addr.arpa" IN {
 type master;
 file "$HOME/rroo/named/127.0.0";
};
zone "midominio.privado" IN {
 type master;
 file "$HOME/rroo/named/midominio.privado";
};
```
El fichero $HOME/rroo/named/127.0.0 tendría el siguiente contenido:
```
$TTL 86400
@       IN      SOA       ns.midominio.privado.       mail.midominio.privado. (
                200403031 ; Numero de Serie: Fecha+Numero
                28800 ; Tiempo de Refresco
                7200 ; Tiempo de Reintento
                604080 ; Caducidad de la informacion
                86400) ; TTL para clientes
        NS      ns.midominio.privado.
1       PTR     localhost.

```

Y el fichero $HOME/rroo/named/midominio.privado tendría el siguiente contenido:
```
$TTL 86400
@       IN      SOA         ns.midominio.privado.       mail.midominio.privado. (
                            200403031
                            28800
                            7200
                            604800
                            86400 )
                NS          ns.midominio.privado.
                MX          10 mail.midominio.privado.
ns              A           192.168.123.1
mail            A           192.168.123.2
www             CNAME       ns
```
### 1. Copie estos ficheros (puede clonarlos de este repositorio), arranque un named en su máquina y compruebe con la herramienta dig que está funcionando correctamente.
```
 git clone https://gitlab.gast.it.uc3m.es/aptel/dns.git
 ```
### 2. Pare el servidor named y modifique los ficheros de configuración para que sirva también el dominio necesario para la resolución inversa.
