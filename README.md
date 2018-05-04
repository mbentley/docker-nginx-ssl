generate a new set of self signed certs

```
openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out cert.pem

Country Name (2 letter code) []:US
State or Province Name (full name) []:Indiana
Locality Name (eg, city) []:Indianapolis
Organization Name (eg, company) []:Docker, Inc.
Organizational Unit Name (eg, section) []:nginx-ssl
Common Name (eg, fully qualified host name) []:nginx.example.com
Email Address []:
```

create secrets
```
docker secret create cert.pem cert.pem
docker secret create key.pem key.pem
```

create network
```
docker network create -d overlay nginx-ssl
```

create service
```
docker service create \
  --name nginx-ssl \
  --network nginx-ssl \
  --secret key.pem \
  --secret cert.pem \
  --label "com.docker.lb.network=nginx-ssl" \
  --label "com.docker.lb.hosts=nginx.example.com" \
  --label "com.docker.lb.port=443" \
  --label "com.docker.lb.ssl_passthrough=true" \
  mbentley/nginx-ssl:latest
```
