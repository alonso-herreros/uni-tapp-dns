[English version] (https://gitlab.pervasive.it.uc3m.es/aptel/dns/blob/master/README_EN.md)

# Práctica de DNS

### Introducción a Dig
Para realizar esta práctica usaremos el programa dig. A continuación resumimos las principales opciones de este comando, pero le recomendamos que consulte la página de manual completa haciendo `man dig`.

La llamada a dig con sus principales opciones son las siguientes (`[]` indica opcional):

dig [@server] [-p port#] [name] [type] [queryopt...] 

* `@server`: nombre o dirección IP del servidor DNS al que se hace la consulta. Si no se especifica, se consulta a los servidores que se listan en el fichero /etc/resolv.conf.
* `-p port`: sirve para indicar un puerto de servidor, distinto del puerto por defecto (53).
* `name`: nombre del registro de recurso que se desea consultar.
* `type`: tipo del registro de recurso que se desea consultar: ANY, A, MX, etc. Si no se especifica ninguno, por defecto es el tipo A.
* `queryopt`: una o más de las siguientes opciones:
..* `+tcp`: para forzar que la consulta se haga por TCP (por defecto UDP, excepto AXFR o IXFR). `+notcp` para forzar UDP
..* `+ignore`: para no reintentar consulta por TCP si la respuesta UDP está truncada.
..* `+norecurse`: para forzar consulta iterativa (por defecto es recursiva).


