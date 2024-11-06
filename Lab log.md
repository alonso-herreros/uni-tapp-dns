## Telematic Applications

# DNS Lab

*Academic year 2024-2025*

---

## Part 1: queries with a DNS client

### 1.1. Determine the IP address of the machine `www.mec.es`.

Its IPv4 address is `212.128.114.29`

### 1.2. Check which machine has the IP address `193.110.128.199`.

That IP is associated with `www.elmundo.es`

### 1.3. Get the name and address of `abc.es` DNS servers (primary & secondary)

These are the name servers available:

```text
abc.es.			19977	IN	NS	a4-67.akam.net.
abc.es.			19977	IN	NS	a1-229.akam.net.
abc.es.			19977	IN	NS	a5-64.akam.net.
abc.es.			19977	IN	NS	a11-64.akam.net.
abc.es.			19977	IN	NS	a18-65.akam.net.
abc.es.			19977	IN	NS	a24-66.akam.net.
```

Luckily for us, their addresses were included in the additional section:

```text
a4-67.akam.net.		11617	IN	A	72.246.46.67
a5-64.akam.net.		11617	IN	A	95.100.168.64
a1-229.akam.net.	85836	IN	A	193.108.91.229
a11-64.akam.net.	11616	IN	A	84.53.139.64
a18-65.akam.net.	11617	IN	A	95.101.36.65
a24-66.akam.net.	11617	IN	A	2.16.130.66
a4-67.akam.net.		11617	IN	AAAA	2600:1480:9000::43
a5-64.akam.net.		11617	IN	AAAA	2600:1480:b000::40
a1-229.akam.net.	19791	IN	AAAA	2600:1401:2::e5
a11-64.akam.net.	11616	IN	AAAA	2600:1480:1::40
a18-65.akam.net.	11617	IN	AAAA	2600:1480:4800::41
a24-66.akam.net.	11617	IN	AAAA	2600:1480:9800::42
```

As stated in the `SoA` record, the primary server is `a1-229.akam.net`

### 1.4. Get SoA of `abc.es` by asking the local DNS and their primary server

As expected, the local DNS doesn't advertise an autoritative answer, but
`abc.es`'s primary server does.

### 1.5. What is the email of the `abc.es` DNS admin?

The admin's email address is `dnsadmin@abc.es`

### 1.6. Find the name and IP address of the admin's mail server

There are several mail servers, all operated by google:

```text
abc.es.			236	IN	MX	10 alt3.aspmx.l.google.com.
abc.es.			236	IN	MX	5 alt2.aspmx.l.google.com.
abc.es.			236	IN	MX	5 alt1.aspmx.l.google.com.
abc.es.			236	IN	MX	10 alt4.aspmx.l.google.com.
abc.es.			236	IN	MX	1 aspmx.l.google.com.
```

We can get their addresses by asking for their A records. I'll do that for the
main one (the one with lower priority):

```text
aspmx.l.google.com.	293	IN	A	74.125.71.26
```

### 1.7. `www.vanguardia.es`'s TTL in local DNS

By going through the root server, resolving to the canonical name, and asking
the authoritative servers in charge, I can see that the maximum TTL is 180
(answered by `ns-716.awsdns-25.net` in an `A` record with `aa`)

However, the canonical name has a different TTL, and is served by someone else.
The TTL is 86400 (answered by `dns01.grupogodo.com` in a `CNAME` record with
`aa`)

### 1.8. Ask `j.root-servers.net` the same thing

If `j.root-servers.net` is asked explicitly (using `@` in the dig command), the
server replies with a list of name servers for the `.es` region, but the flag
`ra` (recursion available) remains unset, meaning the root server is not
willing to do the recursion for us

### 1.9. Find how many computers are doing load balancing in `www.elpais.es`

By doing `dig A` for the requested name, we get that there are 2 IPs associated
with `www.elpais.es` (or, technically, a very long canonical name). They are
the same ones in the tests done, but the order is sometimes changed.

### 1.10. Find `www.pcreview.co.uk` and `www.bb.co.uk`'s addresses iteratively

Using the `+norecurse` option and starting at the root servers to avoid using the local DNS server's cached records, I did that and got the following results:

```text
www.pcreview.co.uk.	300	IN	A	172.67.135.45
www.pcreview.co.uk.	300	IN	A	104.21.6.187
```

```text
www.bb.co.uk.		300	IN	CNAME	bb.co.uk.
bb.co.uk.		300	IN	A	64.91.253.46
```

The full trace can be found in the logs

### 1.11. Do the same using the `+trace` option

This is easier by just executing `dig` with the `+trace` option. The responses
with the full trace can be found in the logs.

### 1.12. Use DNS to check which machines act as `gmail.com`'s mail servers

There is one main server and 4 alt servers:

```text
gmail.com.		2241	IN	MX	5 gmail-smtp-in.l.google.com.
gmail.com.		2241	IN	MX	40 alt4.gmail-smtp-in.l.google.com.
gmail.com.		2241	IN	MX	30 alt3.gmail-smtp-in.l.google.com.
gmail.com.		2241	IN	MX	10 alt1.gmail-smtp-in.l.google.com.
gmail.com.		2241	IN	MX	20 alt2.gmail-smtp-in.l.google.com.
```

Their IPs can be found by asking for the A records associated with those names.
I'll check the address of the primary one (priority `5`)

```text
gmail-smtp-in.l.google.com. 300	IN	A	74.125.133.27
```

### 1.13. How would you get all RRs in `lab.it.uc3m.es`?

By using an `AXFR` query. The output is huge, can be found in the logs

### 1.14. Find which are a zone

* `google.jobs`
* `primevideo.com`
* `inf.uc3m.es`
* `it.uc3m.es`

By querying for `SoA`, we can see which zone the advertised server manages. In
all cases except for `inf.uc3m.es`, the `NAME` field in the `SoA`, which is the
zone name, contains the same address we asked for. In the case of
`inf.uc3m.es`, we get the following answer:

```text
uc3m.es.		8164	IN	SOA	vorteX.uc3m.es. netmaster.uc3m.es. 2024101601 86400 7200 2592000 172800
```

This means the zone `uc3m.es` is managed by `vorteX.uc3m.es`, so we can infer
that `inf.uc3m.es` is not a zone of its own but a subdomain of `uc3m.es`.

### 1.15. Get lots of data from `it.uc3m.es` and `csic.es`

#### 1.15.1. From `it.uc3m.es`

The DNS servers are the following:

```text
it.uc3m.es.		86400	IN	NS	vorteX.uc3m.es.
it.uc3m.es.		86400	IN	NS	varpa.it.uc3m.es.
it.uc3m.es.		86400	IN	NS	tamtam.it.uc3m.es.
```

Where `tamtam.it.uc3m.es` is the primary:

```text
it.uc3m.es.		86400	IN	SOA	tamtam.it.uc3m.es. root.tamtam.it.uc3m.es. 2024101600 7200 3400 604800 600
```

The mail servers are the following (managed by google)

```text
it.uc3m.es.		86400	IN	MX	30 ASPMX3.GOOGLEMAIL.COM.
it.uc3m.es.		86400	IN	MX	10 ASPMX.L.GOOGLE.COM.
it.uc3m.es.		86400	IN	MX	20 ALT2.ASPMX.L.GOOGLE.COM.
it.uc3m.es.		86400	IN	MX	20 ALT1.ASPMX.L.GOOGLE.COM.
it.uc3m.es.		86400	IN	MX	30 ASPMX2.GOOGLEMAIL.COM.
```

The admin's email address can be found in the SoA record above:
`root@tamtam.it.uc3m.es`

The copy, expiration and TTL times can also be found in the SoA record

* Secondary-primary copy (refresh) time: `7200`
* Expiration time: `604800`
* Minimum TTL: `600`

#### 1.15.2. From `csic.es`

The DNS servers are the following:

```text
csic.es.		83556	IN	NS	chico.rediris.es.
csic.es.		83556	IN	NS	sun.rediris.es.
csic.es.		83556	IN	NS	olmo.csic.es.
csic.es.		83556	IN	NS	sabina.cti.csic.es.
```

Where `olmo.csic.es` is the primary:

```text
csic.es.		83493	IN	SOA	olmo.csic.es. hostmaster.csic.es. 2010763741 86400 7200 2592000 3600
```

The only mail server (self-managed) is found below:

```text
csic.es.		68867	IN	MX	10 mx.csic.es.
```

The admin's email address can be found in the SoA record above:
`hostmaster.csic.es`

The copy, expiration and TTL times can also be found in the SoA record

* Secondary-primary copy (refresh) time: `86400`
* Expiration time: `259200`
* Minimum TTL: `3600`

## Part 2

A symlink to the `named` executable in `/usr/dist/sbin` was created in
`$HOME/.local/bin` for easier access.

> **WARNING**
>
> The `named.conf` file was modified to be a template, where the variable
> `NAMED_DIR` holds the absolute path to the `named` folder in this repo. From
> this repo's root, run `export NAMED_DIR=$PWD/named` to give the variable its
> value.
>
> The `named_wrapper` script was created to accept this template and turn it
> into a valid config file by doing environment variable substitution.
>
> This wrapper accepts an option `--template <file>` which transforms the given
> file using `envsubst` and then passes it to `named` along with any other
> arguments received.

### 2.1. Start a `named` and check that it's running with dig

With the changes mentioned above, it all worked

> **NOTE**
>
> If despite all attempts to start `named`, and after checking the config files
> are correct it still won't start, check for the existence of the `named.pid`
> file. The existence of this file in the directory specified in `named.conf`
> will prevent a new `named` from starting.

### 2.2. Modify `named` config to also server inverse resolution

The following entry was added to the `named.conf` file (note that environment
variable substitution is performed by the wrapper script)

```named
zone "123.168.192.in-addr.arpa" IN {
  type master;
  file "$NAMED_DIR/192.168.123";
};
```

Then, in a new file `192.168.123` in the same `named` directory, the following
content was added (mostly copied from the file `127.0.0`, except the last 2
lines)

```named
$TTL 86400
@       IN      SOA     ns.midominio.privado.      mail.midominio.privado. (
                200403031 ; Numero de Serie: Fecha+Numero
                28800 ; Tiempo de Refresco
                7200 ; Tiempo de Reintento
                604080 ; Caducidad de la informacion
                86400) ; TTL para clientes
        NS      ns.midominio.privado.
1       PTR     ns.midominio.privado.
2       PTR     mail.midominio.privado.
```

Essentially, everything worked as expected:

```text
[2024-10-24, 19:18:34] dig -p 10053 PTR 1.123.168.192.in-addr.arpa @localhost

; <<>> DiG 9.18.16-1~deb12u1-Debian <<>> -p 10053 PTR 1.123.168.192.in-addr.arpa @localhost
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 29991
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;1.123.168.192.in-addr.arpa.	IN	PTR

;; ANSWER SECTION:
1.123.168.192.in-addr.arpa. 86400 IN	PTR	ns.midominio.privado.

;; AUTHORITY SECTION:
123.168.192.in-addr.arpa. 86400	IN	NS	ns.midominio.privado.

;; ADDITIONAL SECTION:
ns.midominio.privado.	86400	IN	A	192.168.123.1

;; Query time: 0 msec
;; SERVER: 127.0.0.1#10053(localhost) (UDP)
;; WHEN: Thu Oct 24 19:18:34 CEST 2024
;; MSG SIZE  rcvd: 119

[2024-10-24, 19:18:42] dig -p 10053 PTR 2.123.168.192.in-addr.arpa @localhost

; <<>> DiG 9.18.16-1~deb12u1-Debian <<>> -p 10053 PTR 2.123.168.192.in-addr.arpa @localhost
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 14998
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;2.123.168.192.in-addr.arpa.	IN	PTR

;; ANSWER SECTION:
2.123.168.192.in-addr.arpa. 86400 IN	PTR	mail.midominio.privado.

;; AUTHORITY SECTION:
123.168.192.in-addr.arpa. 86400	IN	NS	ns.midominio.privado.

;; ADDITIONAL SECTION:
ns.midominio.privado.	86400	IN	A	192.168.123.1

;; Query time: 0 msec
;; SERVER: 127.0.0.1#10053(localhost) (UDP)
;; WHEN: Thu Oct 24 19:18:42 CEST 2024
;; MSG SIZE  rcvd: 124
```

