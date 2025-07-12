# MAIL SERVER + WEB MAIL CLIENT

be sure that your server has open port 25 to output. It can be close by vps provider

## set up
1. clone repository
2. install docker ce [link](https://docs.docker.com/engine/install/ubuntu/)
3. run the docker 
```bash
docker network create mailer_network && docker compose up -d
```
4. save the authorization credentials
```bash
docker logs stalwart 
```

for example
```text
✅ Configuration file written to /opt/stalwart/etc/config.toml
🔑 Your administrator account is 'admin' with password 'A3l936UNgj'.
```

## stalwart configurations

1. authorize to the admin [localhost:9000](http://localhost:9000])
2. add hostname 
- settings -> server -> network
Network settings
- Hostname: mail.example.com
- save and reload

It's your mail server smtp and imap server connection host
3. add dns record for mail server

| type | name  | content | proxy status | TTL  |
|------|-------|---------|-------------|------|
| A    | mail |    <YOUR_SERVER_IP>     |    DNS only | auto |
4. next step is to add ACME providers to get SSL/TLS certificates. I use cloudflare
   - create your cloudflare API KEY
   - add ACME provider
5. create domain
6. add domain DNS records
7. create accounts

## rainloop configurations (optional)
--- TODO --- 

## create cloudflare API KEY
1. open [cloudflare profile API tokens](https://dash.cloudflare.com/profile/api-tokens)
2. create token
3. API token templates ->  Edit zone DNS -> use template
4. Zone Resources -> select your domain (example.com)
5. continue to summary -> create token
6. save the created token somewhere 

## add ACME provider
1. open and authorize [localhost:9000](http://localhost:9000])
2. settings -> server -> TLS -> ACME providers -> Create a new ACME provider
3. fill the fields
ACME provider
- Directory Id: <ANY> (letsencrypt)
- Directory URL: https://acme-v02.api.letsencrypt.org/directory
- Challenge type: DNS-01
- Contact Email: <YOUR_EMAIL> (info@example.com)
- Subject names: <YOUR_HOSTNAME> (mail.example.com)
- Default provider: true
DNS settings
- DNS Provider: Cloudflare
- Secret: <YOUR_CLOUDFLARE_API_TOKEN>
4. save and reload

## create domain
1. open and authorize [localhost:9000](http://localhost:9000])
2. management -> directory -> domains -> Create a new domain 
3. input fields
- Domain name: <YOUR_DOMAIN_AFTER_@> (example.com)
- Description <ANY> (example.com)
4. save changes 
## add domain DNS records
1. open and authorize [localhost:9000](http://localhost:9000])
2. management -> directory -> domains -> <DOMAIN> -> "..." -> view dns records
3. open [dash.cloudflare.com/](https://dash.cloudflare.com/)
4. select account 
5. select domain
6. DNS -> add record
7. copy/past at least all required records from stalwart to cloudflare.

### DNS Records for `example.com` (Stalwart Mail Server)

> ✅ Minimum and recommended DNS records for running a secure mail server with Stalwart  
> 🛡️ Compatible with DNS providers like Cloudflare that support DNSSEC

---

Required DNS Records

| Type  | Name                     | Value                                                                                   | Notes                                    |
|-------|--------------------------|------------------------------------------------------------------------------------------|------------------------------------------|
| A     | mail                     | 1.2.3.4                                                                                  | IP address of your mail server           |
| MX    | @                        | mail.example.com. (priority 10)                                                          | Main mail handler                        |
| TXT   | @                        | "v=spf1 ip4:1.2.3.4 -all"                                                                | SPF policy for allowed senders           |
| TXT   | _dmarc                   | "v=DMARC1; p=quarantine; rua=mailto:dmarc@example.com"                                   | DMARC policy                             |
| TXT   | default._domainkey       | "v=DKIM1; k=rsa; p=MIIBIjANBgkqh...QIDAQAB"                                              | DKIM public key                          |
| CNAME | autoconfig               | mail.example.com.                                                                        | Mail client autoconfiguration            |

---
Optional DNS Records

| Type   | Name                         | Value                                      | Notes                                     |
|--------|------------------------------|--------------------------------------------|-------------------------------------------|
| SRV    | _submission._tcp.example.com | 0 1 587 mail.example.com.                  | SMTP with authentication (submission)     |
| SRV    | _imap._tcp.example.com       | 0 1 993 mail.example.com.                  | IMAP over TLS                             |
| SRV    | _pop3._tcp.example.com       | 0 1 995 mail.example.com.                  | POP3 over TLS                             |
| TLSA   | _25._tcp.mail.example.com    | 3 1 1 <TLSA_HASH>                          | DANE record for SMTP                      |
| TLSA   | _465._tcp.mail.example.com   | 3 1 1 <TLSA_HASH>                          | DANE record for SMTPS                     |

---

Notes
- Always test records with tools like [MXToolbox](https://mxtoolbox.com/) or `dig` before deploying.

## create accounts
1. open and authorize [localhost:9000](http://localhost:9000])
2. management -> directory -> accounts -> Create a new account
3. input fields
Details (tab)
- Login name: <LOGIN> (test)
- Login name: <NAME> (test)
- Email: <EMAIL> (test@example.com)
Authorization (tab)
- password <PASSWORD> (test)

