# S-DOEA - Workshop 9 - Container Security - Docker Scout


1. Spin off a digital ocean ubuntu 24.04 (1vcpu 1GB RAM) instance with docker engine and cli installed

https://docs.docker.com/engine/install/ubuntu/


2. Setup docker scout cli on the newly created ubuntu instance (Manual installtion)

```
mkdir ~/.docker
```

```
curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s --
```

3. Git clone down the scout vulnerable demo service server

```
git clone https://github.com/docker/scout-demo-service.git
```

4. Change to the demo service working directory

```
cd scout-demo-service
```


5. Login to the docker repository (Dockerhub), kindly use the dockerhub credential.

```
docker login
```

6. Build the demo server docker image, replace the placeholder with your own dockerhub username

```
docker build --push -t <dockerhub username>/scout-demo-<grp number>:v1 .
```


7. Scan docker image with known vulnerabilities, filter only check with a certain package

```
docker scout cves --only-package express
```

<br>
<img style="float: center;" src="./screens/scout1.png">
<br>

<br>
<img style="float: center;" src="./screens/scout2.png">
<br>


```
docker scout recommendations kenken64/scout-demo-<grp number>:v1
```

<br>
<img style="float: center;" src="./screens/scout3.png">
<br>


8. Edit the package.json file by upgrading the express library to specific version


```
nano package.json
```

```
"dependencies": {
    "express": "4.19.2"
}
```

9. Install NPM before reinstall the express library

```
apt install npm
```

```
npm i
```

10. Rebuild the docker image and push to dockerhub

```
docker build --push -t <dockerhub username>/scout-demo-<grp number>:v2 .

```

<br>
<img style="float: center;" src="./screens/scout4.png">
<br>


11. Re-scan for known vulnerablities on the express library. The result of this scan will show all cves are fixed. Upload this screenshot as submission

```
docker scout cves --only-package express
```

<br>
<img style="float: center;" src="./screens/scout5.png">
<br>


12. Evaluate policy compliance within the containers


```
docker scout quickview
```
<br>
<img style="float: center;" src="./screens/scout6.png">
<br>


13. This will show you critical compliance issues, non default non root user found

14. Resolution to the above issues. Edit your Dockerfile

```
nano Dockerfile
```

```
CMD ["node", "/app/app.js"]
EXPOSE 3000
USER appuser
```
15. Before building a new and push the new docker image to the repository, we need to enable container store for docker engine

```
nano /etc/docker/daemon.json
```

16. Paste the following content to the json file
```
{
    "features": {
        "containerd-snapshotter": true
    }
}
```

17. Follow by a restart on the docker engine, follow by a rebuild of the image to v3

```
systemctl restart docker
```

```
docker build --provenance=true --sbom=true --push -t <dockerhub username>/scout-demo-<grp number>:v3 .

```

18. Please review your Docker Scout dashboard or the compliance quickview. Upload your final Dockerfile to the submission folder, ensuring that all previously mentioned issues have been fully resolved. Refer to the screenshots below for guidance.

```
docker scout quickview
```
19. Edit the Dockerfile

```
FROM alpine:latest

ENV BLUEBIRD_WARNINGS=0 \
  NODE_ENV=production \
  NODE_NO_WARNINGS=1 \
  NPM_CONFIG_LOGLEVEL=warn \
  SUPPRESS_NO_CONFIG_WARNING=true

RUN apk add --no-cache \
  nodejs
RUN apk update && apk upgrade
COPY package.json ./

RUN  apk add --no-cache npm \
 && npm i --no-optional \
 && npm cache clean --force \
 && apk del npm

COPY . /app

CMD ["node","/app/app.js"]

EXPOSE 3000
USER appuser
```
20. Run the following to fix the remaining vulnerability

```
npm audit fix --force
```

21. Publish the final docker image to the hub. Make sure there isnt any remaining vulnerability

```
docker build --provenance=true --sbom=true --push -t <dockerhub username>/scout-demo-<grp number>:v4 .
```

<br>
<img style="float: center;" src="./screens/scout7.png">
<br>

22. Enable the scout scanning , navigate the repository seetings

<br>
<img style="float: center;" src="./screens/scout14.png">
<br>


<br>
<img style="float: center;" src="./screens/scout8.png">
<br>

<br>
<img style="float: center;" src="./screens/scout9.png">
<br>

<br>
<img style="float: center;" src="./screens/scout10.png">
<br>

<br>
<img style="float: center;" src="./screens/scout11.png">
<br>

<br>
<img style="float: center;" src="./screens/scout12.png">
<br>

<br>
<img style="float: center;" src="./screens/scout13.png">
<br>
