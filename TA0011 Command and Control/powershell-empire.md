# Powershell Empire

## Docker instalation

```
# Pull the latest image
docker pull bcsecurity/empire:latest

# Run the server with the rest api and socket ports open
docker run -it -p 1337:1337 -p 5000:5000 bcsecurity/empire:latest

# Run the client
docker run -it -p 1337:1337 -p 5000:5000 bcsecurity/empire:latest client

# To run the client against the already running server container
docker container ls
docker exec -it {container-id} ./ps-empire client

# with persistent storage
docker pull bcsecurity/empire:latest
docker create -v /empire --name data bcsecurity/empire:latest
docker run -it -p 1337:1337 -p 5000:5000 --volumes-from data bcsecurity/empire:latest

# if you prefer to be dropped into bash instead of directly into empire
docker run -it -p 1337:1337 -p 5000:5000 --volumes-from data --entrypoint /bin/bash bcsecurity/empire:latest
```
