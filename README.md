# Traefik web host on Docker Swarm

A simple [Traefik](https://hub.docker.com/_/traefik) web hosting architecture orchestrated by [Docker Swarm](https://docs.docker.com/engine/swarm/). Designed for quick and easy hosting and routing of multiple self-contained [Docker](https://www.docker.com) images. This architecture offers a straightforward approach to link URLs to containerized applications. All incoming HTTP traffic is rerouted to a secure and encrypted HTTPS connection certified by [Let's encrypt](https://letsencrypt.org).



## Requirements

- **Linux web server** - DigitalOcean droplet or similar.
- **IPv4** - IP address to the web server.
- **Docker** - Up to date version of Docker running on server.
- **Docker image** - Dockerised web application hosted on [Docker Hub](https://hub.docker.com/).
- **Domain name** - URL routed to the web server.



## Setup

1) Create the required directories for storing TSL certificates and logs (can be relocated in `traefik/docker-compose.yml`).
```
mkdir logs && mkdir letsencrypt
```

2) Set the email and IP environment variables to your information.
```
export WEBHOST_EMAIL=email@example.com      # Your email address
export WEBHOST_IP=192.168.0.1               # IP to server
```   

3) Initiate the Docker Swarm cluster.
```
docker swarm init --advertise-addr ${WEBHOST_IP}
```

4) Deploy the Traefik container.
```
docker stack deploy -c traefik/docker-compose.yml webhost
```

5) Set the website environmental variables and deploy website container.
```
export WEBSITE_SERVICE=website1           # Any unique tag 
export WEBSITE_IMAGE=nginx                # Image name on Docker Hub
export WEBSITE_URL=myurl.com              # Without www

docker stack deploy -c website/docker-compose.yml ${WEBSITE_SERVICE}
```

6) Repeat step 5 for every website to be deployed.
7) When ready to deploy, change the Let's Encrypt URL in `traefik/docker-compose.yml` to deployment mode, and redeploy webhost container.
```
FROM:   https://acme-staging-v02.api.letsencrypt.org/directory
TO:     https://acme-v02.api.letsencrypt.org/directory

docker stack deploy -c traefik/docker-compose.yml webhost
```
