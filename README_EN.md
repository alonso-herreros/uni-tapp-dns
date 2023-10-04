# DNS Assignment 

### Introduction: The dig utility
To carry out this practical assignment we will use the dig utility. Below we resume the main options of
this command but we recommend you consult the complete manual page, which can be obtained via the
command man dig. If you prefer you may also use the host or nslookup utilities which are similar to dig.
The main options of dig are the following  (`[]` stands for optional):
```
dig [@server] [-p port#] [name] [type] [queryopt...] 
```
* `@server`:  specifies the name or IP address of the DNS servers to which the query is to be made; if none is specified, the servers listed in the file /etc/resolv.conf are used. 
* `-p port`: is used to indicate a server port in the case where a port different from the default (53) is to be used. 
* `name`:  specifies the name of the resource registry to be consulted. 
* `type`: specifies the type of the resource registry to be consulted: ANY, A, MX, etc.; if none is given, it is assumed to be of type A. 
* `queryopt`:  one or more of the following options:
..* `+tcp`: used to force the query to be made via TCP (by default UDP is used, except for AXFR or IXFR).  +notcp to force UDP. 
..* `+ignore`:  specifies not to retry the query via TCP if the UDP reply is truncated. 
..* `+norecurse`: used to force an iterative query (the default is recursive). 

The assignment is divided in two parts:
* in the first we will use dig to query resources from the dns servers
* in the second one we will setup our own DNS server using `named`

## Part 1: Queries with a DNS client

Remember the format of a resource record (RR) in DNS is the following:

```
<domain name> <TTL> IN <record type> <value> 
```

### 1. Determine the IP address of the machine `www.mec.es`. 

### 2. Check which machine has the IP address `193.110.128.199`. 

### 3. Find out the name and IP address of the DNS servers of the domain `abc.es` and say which of them is primary and which is secondary. 

### 4. Obtain the SOA registry of the domain `abc.es`, first, by asking the local DNS and, second, by asking the primary server of the abc.es domain. Verify that in one case, the response is authoritative and in the other, it isn't. 

### 5. If you had a problem with the DNS of `abc.es` and you had to send an e-mail to its administrator, to what address would you send it? 

### 6. Determine the name and IP address of the mail server of the administrator referred to in the previous question

### 7. How long will the IP address of `www.vanguardia.es` remain in the cache of your local DNS? Ask your local DNS for this address several times in succession. What do you observe in the TTL of the resource registry? 

### 8. Now ask the same to a root server (for example, `J.ROOT-SERVERS.NET` with IP address `192.58.128.30`) and check in the reply packet that this server does not accept the recursive mode. 

### 9. Find out how many computers are carrying out load balancing in the web server `www.elpais.es`. Do you always get the same ones in the same order? 

### 10. By making iterative queries, check the IP address of `www.pcreview.co.uk`. What are the steps you have taken? 

> Hint: if your DNS server has this record in the cache it is possible it does not answer with the next step but with the results. In that case, ask directly to a root server in this way: `dig +norecurse www.pcreview.co.uk @A.ROOT-SERVERS.NET`, and continue. 

Following the same steps (iterative queries), do you obtain the IP address of `www.bbc.co.uk`? 


### 11. The same can be done with the +trace option of dig. Check the result of doing so. 

### 12. Using the information available via DNS determine the computer or computers (name and IP address) that act as mail servers for the domain `gmail.com`.

### 13. What would you need to do to obtain all the resource registries of the the zone `lab.it.uc3m.es`? 

## Part 2: Creating a domain in a named DNS server 

### Configuring the named DNS server

`named`  is the DNS server that forms part of the most widely-used implementation of DNS in Unix, BIND
(Berkeley Internet Name Domain). We are going to use the version of named that can be found in the
`directory /usr/dist/sbin/`.

> Warning: it may happen the execution shows an error or warning regarding a library that cannot be loaded. This happens due to the specific library distribution in the lab since 32 bit versions co-exist with 64 versions but it should work despite the error

> Alternativerly, there is a named implementation in `/usr/sbin`

On start-up, `named`  reads a configuration file, located by default at `/etc/bind/named.conf`, though we can specify a different file via the command line. Use of the option `-f` is also convenient for this practical (see
the named manual page). We will therefore start named using the following command (from the folder  in which our config file is located): 

```
/usr/dist/sbin/named -c ./named.conf -f
```

The file named.conf is divided into two parts. 

* The options section is used to specify configuration aspects such as the port on which named will listen
(on startup in user mode it cannot be 53, we will need to specify a non-reserved port such as 10053)
and the locaction of certain files that it will need to write during operation (which we will set to be in our
account). 
* The zone section is used to define one or more DNS zones that this server is responsible for.

An example of a configuration file (where we are supposing that you are working in the subdirectory `rroo/named` of your account; substitute `$HOME` by the complete path of your HOME) is as follows: 

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
In this case, the file `$HOME/rroo/named/127.0.0` contains the following: 

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

and the file `$HOME/rroo/named/midominio.privado` contains the following: 
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
### 1. Copy the above files, start a named on your computer and use the dig tool to check that it is working properly. Use the following to retrieve all the files:
```
 git -c http.sslVerify=false clone https://gitlab.gast.it.uc3m.es/aptel/dns.git
```
### 2. Stop the named server and modify the configuration files so that it also serves the domain necessary for inverse resolution. 
