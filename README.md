# Traefik proxy

This is our repository for Traefik proxy containers. Traefik is, you guessed, a proxy. It listens to port 80 and proxy all network traffic to your containers.
You just need to set some labels, add a network, and you can have multiple web applications running at once.

## Getting started

run traefik with

```
docker-compose up -d
```
it will be available on port 80. The dashboard will be at ```https://traefik.docker.localhost/dashboard/#/``` 
note: you should set up SSL for this to work!

## Configuring your application

Expose your application by adding these labels:
```
labels:
  - traefik.enable=true
  - traefik.docker.network=proxy
  - traefik.http.routers.[your-site-name].rule=Host(`$SITE_NAME.docker.localhost`)
  - traefik.http.routers.[your-site-name].tls=true
```
add an external network named "proxy" in your docker-compose.yml
```
networks:
    ...
    proxy:
      external: true
    ...
```
Also, please remember to remove port mapping or you'll get a conflict for port 80.
Now run you containers and you'll be alright!

Isn't it a thing of beauty? We think it is.

## SSL? there you go:

please install mkcert first

```
curl -JLO "https://dl.filippo.io/mkcert/latest?for=linux/amd64"
chmod +x mkcert-v*-linux-amd64
sudo cp mkcert-v*-linux-amd64 /usr/local/bin/mkcert
```
then you have to "trick" your browsers into trusting a self signed certificate with:
```
mkcert -install
``` 
then you can generate an ssl certificate for every domain you want. These are the one we're using:
```
mkcert -cert-file certs/local-cert.pem -key-file certs/local-key.pem "docker.localhost" "*.docker.localhost" "*.local"  "*.wp.test" "*.mysql.localhost" "*.mailhog.localhost"
```
