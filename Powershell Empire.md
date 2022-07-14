# Powershell Empire

## Docker instalation

```
# Pull the latest image
sudo docker pull bcsecurity/empire:latest

# Run the server with the rest api and socket ports open
sudo docker run -it -p 1337:1337 -p 5000:5000 bcsecurity/empire:latest

# Run the client
sudo docker run -it -p 1337:1337 -p 5000:5000 bcsecurity/empire:latest client

# To run the client against the already running server container
sudo docker container ls
sudo docker exec -it {container-id} ./ps-empire client

# with persistent storage
sudo docker pull bcsecurity/empire:latest
sudo docker create -v /empire --name data bcsecurity/empire:latest
sudo docker run -it -p 1337:1337 -p 5000:5000 --volumes-from data bcsecurity/empire:latest

# if you prefer to be dropped into bash instead of directly into empire
sudo docker run -it -p 1337:1337 -p 5000:5000 --volumes-from data --entrypoint /bin/bash bcsecurity/empire:latest
```
