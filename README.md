# jenkins

## Build and Run Docker Image

```sh
docker build -t jenkins-blueocean:2.462.1 .

docker run --name jenkins-blueocean --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  jenkins-blueocean:2.462.1
```

## Setup Cloud Agent - Docker

### Run `apache/socat` to forward traffic from Jenkins to Docker on Host Machine

ref: https://stackoverflow.com/questions/47709208/how-to-find-docker-host-uri-to-be-used-in-jenkins-docker-plugin

```sh
docker run -d --restart=always -p 127.0.0.1:2376:2375 \
  --network jenkins -v /var/run/docker.sock:/var/run/docker.sock \
  alpine/socat tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock

docker inspect <container_id> | grep IPAddress

# put tcp://<ip_address>:2375 in UI
```

![image](https://github.com/user-attachments/assets/8bc36d5c-adc3-4ce1-80a3-6090b4caadc0)


