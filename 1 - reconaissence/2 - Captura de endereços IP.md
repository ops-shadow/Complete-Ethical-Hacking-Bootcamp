# Captura de endereços IP

## Comando ping
A forma mais simples é através do comando ping

`$ ping {domínio}`

*Exemplo:* 
```
$ ping google.com
PING google.com (142.250.219.142) 56(84) bytes of data.
64 bytes from tzrioa-aj-in-f14.1e100.net (142.250.219.142): icmp_seq=1 ttl=117 time=5.97 ms
64 bytes from tzrioa-aj-in-f14.1e100.net (142.250.219.142): icmp_seq=2 ttl=117 time=6.03 ms
64 bytes from tzrioa-aj-in-f14.1e100.net (142.250.219.142): icmp_seq=3 ttl=117 time=9.27 ms
^C
--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 5.967/7.088/9.271/1.543 ms
```
No exemplo o IP do Google é *142.250.219.142*.

## Comando nslookup
`$ nslookup {domínio}`

*Exemplo:*
```
$ nslookup google.com
Server:         192.168.1.254
Address:        192.168.1.254#53

Non-authoritative answer:
Name:   google.com
Address: 142.250.219.142
Name:   google.com
Address: 2800:3f0:4004:80f::200e
```
Além do endereço IP, o camando também retorna o endereço MAC

## Comando dig
`$ dig {domínio}`

*Exemplo:*
```
$ dig google.com

; <<>> DiG 9.20.11-4-Debian <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 46648
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             174     IN      A       142.250.219.142

;; Query time: 7 msec
;; SERVER: 192.168.1.254#53(192.168.1.254) (UDP)
;; WHEN: Fri Aug 15 18:53:20 -03 2025
;; MSG SIZE  rcvd: 55
```
## Comando whois
`$ whois {domínio}`

Fornece informações do website whois, incluindo informações de registro, DNS, etc.

## Outras opções

Websites como o whois.com, nslookup.io, iplocation.io, etc.

