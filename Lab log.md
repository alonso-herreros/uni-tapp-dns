## Telematic Applications

# DNS Lab

*Academic year 2024-2025*

---

## Part 1: queries with a DNS client

### Question 1.1

Determine the IP address of the machine `www.mec.es`.

### Question 1.2

Check which machine has the IP address `193.110.128.199`.

### Question 1.3

Find out the name and IP address of the DNS servers of the domain `abc.es` and
say which of them is primary and which is secondary.

### Question 1.4

Obtain the SOA registry of the domain `abc.es,` first, by asking the local DNS
and, second, by asking the primary server of the `abc.es` domain. Verify that
in one case, the response is authoritative and in the other, it isn't.

### Question 1.5

If you had a problem with the DNS of `abc.es` and you had to send an e-mail to
its administrator, to what address would you send it?

### Question 1.6

Determine the name and IP address of the mail server of the administrator
referred to in the previous question

### Question 1.7

How long will the IP address of `www.vanguardia.es` remain in the cache of your
local DNS? Ask your local DNS for this address several times in succession.
What do you observe in the TTL of the resource registry?

### Question 1.8

Now ask the same to a root server (for example, `J.ROOT-SERVERS.NET` with IP
address `192.58.128.30`) and check in the reply packet that this server does
not accept the recursive mode.

### Question 1.9

Find out how many computers are carrying out load balancing in the web server
`www.elpais.es.` Do you always get the same ones in the same order?

### Question 1.10

By making iterative queries, check the IP address of `www.pcreview.co.uk.`
What are the steps you have taken?

Following the same steps (iterative queries), do you obtain the IP address of
`www.bbc.co.uk`?

### Question 1.11

The same can be done with the `+trace` option of dig. Check the result of
doing so.

### Question 1.12

Using the information available via DNS determine the computer or computers
(name and IP address) that act as mail servers for the domain `gmail.com`.

### Question 1.13

What would you need to do to obtain all the resource registries of the the zone
`lab.it.uc3m.es`?

### Question 1.14

Find out which of the following domain names are a zone: `google.jobs`,
`primevideo.com`, `inf.uc3m.es`, `it.uc3m.es`.

### Question 1.15

Identify the DNS servers, mail servers, the domain administrator's address, and
identify the secondary-primary copy times, expiration time, as well as the
minimum TTL for the domains `it.uc3m.es` and `csic.es`.

## Part 2

### Question 2.1

Start a `named` on your computer and use the dig tool to check that it is
working properly. Use the following to retrieve all the files:

### Question 2.2

Stop the named server and modify the configuration files so that it also serves
the domain necessary for inverse resolution.

