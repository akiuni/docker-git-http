# docker-git-http

## Purpose 

This little image creates a git http server

## Build

```
docker build .
```

## Usage

Create a `.write.access` and a `.read.access` files containing RW and RO allowed users :
```
htpasswd -B $(pwd)/.write.access <username>
htpasswd -B $(pwd)/.read.access <username>
```

Create a new local repo writable by the apache user (33 on debian):
```
mkdir -p $(pwd)/repos ; cd $(pwd)/repos
for REPO in repo1.git repo2.git repo3.git; do 
    mkdir ${REPO} && cd ${REPO} && git --bare init && git update-server-info && cd ..
done
chown -R 33:33 $(pwd)/repos
chmod -R 755  $(pwd)/repos
```

Execute the container with 
```
docker run -d --name="git-http" \
--mount type=bind,src=$(pwd)/.read.access,dst=/etc/nginx/git-http/.read.access,readonly \
--mount type=bind,src=$(pwd)/.write.access,dst=/etc/nginx/git-http/.write.access,readonly \
--mount type=bind,src=$(pwd)/repos,dst=/var/lib/git \
-p 80:80 akiuni/git-http
```
