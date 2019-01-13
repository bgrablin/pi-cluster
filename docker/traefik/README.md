Dockerfile
We will be running Traefik on Alpine 3.8:

Build and Push your image to your registry of choice:

$ docker build -t your-user/repo:tag .
$ docker push your-user/repo:tag

Create the overlay network:

$ docker network create --driver overlay traefik