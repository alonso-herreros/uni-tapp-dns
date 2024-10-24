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

As expected, the 

### 1.5. What is the email of the `abc.es` DNS admin?

### 1.6. Find the name and IP address of the admin's mail server

### 1.7. `www.vanguardia.es`'s TTL in local DNS

### 1.8. Ask `j.root-servers.net` the same thing

### 1.9. Find how many computers are doing load balancing in `www.elpais.es`

### 1.10. Find `www.pcreview.co.uk` and `www.bb.co.uk`'s addresses iteratively

### 1.11. Do the same using the `+trace` option

### 1.12. Use DNS to check which machines act as `gmail.com`'s mail servers

### 1.13. How would you get all RRs in `lab.it.uc3m.es`?

### 1.14. Find which are a zone

* `google.jobs`
* `primevideo.com`
* `inf.uc3m.es`
* `it.uc3m.es`

### 1.15. Get lots of data from `it.uc3m.es` and `csic.es`

## Part 2

### 2.1. Start a `named` and check that it's running with dig

### 2.2. Modify `named` config to also server inverse resolution

