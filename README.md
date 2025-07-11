# MAIL SERVER + WEB MAIL CLIENT

be sure that your server has open port 25 to output. It can be close by vps provider

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

5. authorize to the admin [localhost:9000](http://localhost:9000])
6. add hostname settings -> server -> network -> Network settings -> Hostname
mail.example.com
It's your mail server smtp and imap server connection host
7. add dns record for mail server

| type | name  | content | proxy status | TTL  |
|------|-------|---------|-------------|------|
| A    | mail |    <YOUR_SERVER_IP>     |    DNS only | auto |
8. 