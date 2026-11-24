# S-DOEA - Workshop 3 - Containers and Container Management

## Pre-requisite
* AWS Account 
* Dockerhub Account
* Github Account
* Reference material - https://gitlab.com/kenken64/docker-3tier-ecs 
* AWS Region: Singapore

## Test out the Docker installation on the AWS server

Provision Ubuntu server using your own AWS account 

<img src="./screens/aws-ubuntu-1.png" width="400" height="200">

Install docker engine using the below web link

https://docs.docker.com/engine/install/ubuntu/

1. Test out whether docker cli is install correctly

```
docker -v
```

2. Test out whether docker compose is installed correctly. If not kindly follow this installation guide (Linux , plugin method) (https://docs.docker.com/compose/install/#install-compose)

```
docker compose version
```

3. Create the docker group.

```
$ sudo groupadd docker
```

4. Add your user to the docker group. Refresh the user's profile

```
sudo usermod -aG docker $USER

```

Refresh the user profile by issuing the below command

```
source ~/.bashrc
```

5. Verify that you can run docker commands without sudo. if you run into permission denied issue when running the below command kindly logout and ssh back to the slave server.

```
$ docker run hello-world
```

This command downloads a test image and runs it in a container. When the container runs, it prints an informational message and exits.

If you initially ran Docker CLI commands using sudo before adding your user to the docker group, you may see the following error, which indicates that your ~/.docker/ directory was created with incorrect permissions due to the sudo commands.
```
WARNING: Error loading config file: /home/user/.docker/config.json -
stat /home/user/.docker/config.json: permission denied
```

To fix this problem, either remove the ~/.docker/ directory (it is recreated automatically, but any custom settings are lost), or change its ownership and permissions using the following commands:

```
$ sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
$ sudo chmod g+rwx "$HOME/.docker" -R
```

Relogin your Notebook terminal

## Dockerized a sample web app

<img src="../container/images/img16.png" width="400" height="200">

<img src="../container/images/img17.png" width="400" height="200">

1. Clone source code repo from https://github.com/kenken64/reactjs-subdevice.git on the /home/ubuntu (your Slave server). Checkout to the development branch.

```
$ git clone https://github.com/kenken64/reactjs-subdevice.git

$ cd reactjs-subdevice

$ git checkout development

```

2. Create a Dockerfile.test under the React App (subsdevices) on the root working directory

```
FROM node:14-alpine

WORKDIR '/app'

COPY package.json ./
RUN npm install

COPY ./ ./

CMD ["npm", "run", "start"]
```

3. Build the docker image

```
$ docker build -f Dockerfile.test -t kenken64/react-app .
```

4. Run the docker image as container with port forward and volume mounting, once is up and running. 

```
$ docker run -d -p 3000:3000 -v /app/node_modules -v $(pwd):/app kenken64/react-app
```

In order to exit the container , issue the subcommand ps to look for the container id and stop it

```
$ docker ps

$ docker stop <container id>
```

5. Create a docker-compose.yml

```
version: '3'
services:
    web:
      build:
        context: .
        dockerfile: Dockerfile.test
      ports:
        - "3000:3000"
      volumes:
        - /app/node_modules
        - .:/app
```

6. Start the docker container using docker compose.

```
docker compose up -d --build
```

7. Execute the following command. Implement test on separate container, please replace the placeholder value of the container id on the exec command.

```
$ docker ps 
$ docker exec -it <web container id from docker ps> sh
# npm run test
```

In order to exit the shell out back to the host OS, type exit on the container shell

```
# exit
```

- Add test service in the docker compose yml file, save the yml

```
version: '3'
services:
    web:
      build:
        context: .
        dockerfile: Dockerfile
      ports:
        - "3000:3000"
      volumes:
        - /app/node_modules
        - .:/app
    test:
      build:
        context: .
        dockerfile: Dockerfile.test
      volumes:
          - /app/node_modules
          - .:/app
      command: ["npm", "run", "test"]
```

- Stop the container by using the below command

```
$ docker compose stop
```

8. Let's continue building a multi step build process, different base images, create a Dockerfile file and copy paste the below to the Dockerfile

```
# builder phase
FROM node:14-alpine as builder

WORKDIR '/app'

COPY package.json ./
RUN npm install
COPY ./ ./
RUN npm run build

FROM nginx
EXPOSE 80
COPY --from=builder /app/build /usr/share/nginx/html
```

9. Rebuild and Start the docker container using docker compose, in order to incorporate the test service.

```
$ docker compose up -d --build
```

Check whether the container is up and running by issueing the below command

```
$ docker ps
```

Once both the services is running stop the container

```
$ docker compose stop

```


10. Build the multi phase container setup, DO NOT terminate this process. Wait till the following show up Successfully built ```<image id>```

```
docker build .
```

11. Start the multi phase container setup and expose the port, please replace the hash value of the container id from step 10

```
$ docker run -d -p 80:80 <image id>
```
In order check whether the docker is running, list the running container

```
$ docker ps
```


12. Launch your browser and try accessing the app http://```<aws ec2 slave server public DNS>```

13. Remember to register a docker hub account & verify your email before logging to docker hub via the CLI

```
$ sudo apt install gnupg2 pass
$ docker login
```

```
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: <your dockerhub username>
Password:
WARNING! Your password will be stored unencrypted in /home/bunnyppl/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

14. Retrieve the react web docker Id from cli

```
$ docker images
```

15. Tag the react web image

```
$ docker tag <docker ps image id> <your dockerhub userid>/subsdevices:v1
```

16. Push the tagged image to the docker hub

```
$ docker push <your dockerhub userid>/subsdevices:v1
```

```
The push refers to repository [docker.io/kenken64/subsdevices]
82674fe9a8e6: Pushed
6f5e00ced6e0: Pushed
86865100bc00: Pushed
7e93be41b55d: Pushed
1c07e18a989b: Mounted from library/node
b92d384cdf06: Mounted from library/node
a464c54f93a9: Mounted from library/node
v1: digest: sha256:cac661266d1cf19ae4e72f8294e332275a4761a9f5bebe1fd663b1bc3a3c1d9a size: 1788
```

17. Rememer to stop all the container process after you have published the images to the registry.

```
$ docker ps 

$ docker stop <container id>
```

## Optional workshop (Only on MacOs and Windows)

18. Create an account with snyk platform

19. How can we scan vulnerabilities for a containerized app that uses Docker?

* Hint below

```
docker scan --login --token <synk token >
```

```
docker scan <docker tag name>
```

<img style="width:550px;height:300px; float: center;" src="./screens/synk.png"/>
