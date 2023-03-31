# docker-git-http

## Purpose 

This little image creates a git http server

## Build

```
docker build .
```

## Usage

Create a `.htpasswd` file containing the allowed users :
```
htpasswd -B $(pwd)/.htpasswd <username>
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
docker run -d --name="git-http" --mount type=bind,src=$(pwd)/.htpasswd,dst=/etc/nginx/git-http/.htpasswd,readonly --mount type=bind,src=$(pwd)/repos,dst=/var/lib/git -p 80:80 akiuni/git-http
```
