Much detailed instructions can be found here - https://sleepysoul.cc/traefik-reverse-proxy/

**BELOW LABELS ARE TO BE USED FOR OTHER CONTAINERS WHICH NEEDED TO BE MONITORED AND PROXIED BY TRAEFIK**

PS - Never add ports in config when using Traefik

Generate htpasswd using below command

    echo $(htpasswd -nb USERNAME PASSWORD) | sed -e s/\\$/\\$\\$/g

TRAEFIK LABELS
```
     labels:
      - "traefik.enable=true"
      ## replace `service-name` with the name of your docker container, you will need to replace this entry in all lines
      - "traefik.http.routers.service-name.entrypoints=http"
      ## replace `example.domain.tld` with your own domain name, for example `wordpress.domain.ch`
      - "traefik.http.routers.service-name.rule=Host(`example.domain.tld`)"
      - "traefik.http.middlewares.service-name-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.service-name.middlewares=service-name-https-redirect"
      - "traefik.http.routers.service-name-secure.entrypoints=https"
      ## replace `example.domain.tld` with your own domain name, for example `wordpress.domain.ch`
      - "traefik.http.routers.service-name-secure.rule=Host(`example.domain.tld`)"
      - "traefik.http.routers.service-name-secure.tls=true"
      - "traefik.http.routers.service-name-secure.tls.certresolver=http"
      ## replace `service-name` with the name of your docker container
      - "traefik.http.routers.service-name-secure.service=service-name"
      ## if your container is using a different port then `80`, just replace this port with your custom one, for example: `8888`
      - "traefik.http.services.service-name.loadbalancer.server.port=80"
      - "traefik.docker.network=proxy"
```
Make sure your container is in the same network as traefik. You can do this by adding the following to your docker-compose.yml
```
   service-name:
     networks:
       - proxy
networks:
  proxy:
    external: true
```
