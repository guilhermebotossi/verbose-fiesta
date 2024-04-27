## What is the registrar IANA ID number for the githubapp.com domain?
```
$ whois githubapp.com|grep IANA
   Registrar IANA ID: 292
```
----

## What is the last mailserver returned when querying the MX records for githubapp.com?

```
$ dig mx githubapp.com

; <<>> DiG 9.19.17-2~kali1-Kali <<>> mx githubapp.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 666
;; flags: qr rd ra; QUERY: 1, ANSWER: 7, AUTHORITY: 0, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;githubapp.com.			IN	MX

;; ANSWER SECTION:
githubapp.com.		3600	IN	MX	30 aspmx5.googlemail.com.
githubapp.com.		3600	IN	MX	10 aspmx.l.google.com.
githubapp.com.		3600	IN	MX	20 alt1.aspmx.l.google.com.
githubapp.com.		3600	IN	MX	20 alt2.aspmx.l.google.com.
githubapp.com.		3600	IN	MX	30 aspmx2.googlemail.com.
githubapp.com.		3600	IN	MX	30 aspmx3.googlemail.com.
githubapp.com.		3600	IN	MX	30 aspmx4.googlemail.com.

;; ADDITIONAL SECTION:
alt2.aspmx.l.google.com. 226	IN	A	142.250.27.27

;; Query time: 24 msec
;; SERVER: 192.168.15.1#53(192.168.15.1) (UDP)
;; WHEN: Mon Dec 18 09:17:42 -03 2023
;; MSG SIZE  rcvd: 234

```

----

## Perform active infrastructure identification against the host https://i.imgur.com. What server name is returned for the host?

```
$ curl https://i.imgur.com -v
*   Trying 151.101.12.193:443...
* Connected to i.imgur.com (151.101.12.193) port 443
* ALPN: curl offers h2,http/1.1
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
*  CAfile: /etc/ssl/certs/ca-certificates.crt
*  CApath: /etc/ssl/certs
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.2 (IN), TLS handshake, Certificate (11):
* TLSv1.2 (IN), TLS handshake, Server key exchange (12):
* TLSv1.2 (IN), TLS handshake, Server finished (14):
* TLSv1.2 (OUT), TLS handshake, Client key exchange (16):
* TLSv1.2 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.2 (OUT), TLS handshake, Finished (20):
* TLSv1.2 (IN), TLS handshake, Finished (20):
* SSL connection using TLSv1.2 / ECDHE-RSA-AES128-GCM-SHA256
* ALPN: server accepted h2
* Server certificate:
*  subject: CN=*.imgur.com
*  start date: Mar 13 00:00:00 2023 GMT
*  expire date: Mar 12 23:59:59 2024 GMT
*  subjectAltName: host "i.imgur.com" matched cert's "*.imgur.com"
*  issuer: C=GB; ST=Greater Manchester; L=Salford; O=Sectigo Limited; CN=Sectigo RSA Domain Validation Secure Server CA
*  SSL certificate verify ok.
* using HTTP/2
* [HTTP/2] [1] OPENED stream for https://i.imgur.com/
* [HTTP/2] [1] [:method: GET]
* [HTTP/2] [1] [:scheme: https]
* [HTTP/2] [1] [:authority: i.imgur.com]
* [HTTP/2] [1] [:path: /]
* [HTTP/2] [1] [user-agent: curl/8.4.0]
* [HTTP/2] [1] [accept: */*]
> GET / HTTP/2
> Host: i.imgur.com
> User-Agent: curl/8.4.0
> Accept: */*
> 
< HTTP/2 302 
< retry-after: 0
< location: https://imgur.com/
< accept-ranges: bytes
< date: Mon, 18 Dec 2023 12:19:34 GMT
< x-served-by: cache-pdk-katl1840064-PDK
< x-cache: HIT
< x-cache-hits: 0
< x-timer: S1702901974.284457,VS0,VE0
< strict-transport-security: max-age=300
< access-control-allow-methods: GET, OPTIONS
< access-control-allow-origin: *
< server: cat factory 1.0
< content-length: 0
< 
* Connection #0 to host i.imgur.com left intact

```

----

## Perform subdomain enumeration against the target githubapp.com. Which subdomain has the word 'triage' in the name?

```
$ export TARGET=githubapp.com
$ curl -s "https://crt.sh/?q=${TARGET}&output=json" | jq -r '.[]'| grep -m 1 triage
  "common_name": "data-triage-reports.githubapp.com",

```