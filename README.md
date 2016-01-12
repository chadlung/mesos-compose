# Mesos Cluster inside of Docker (for testing)

This Docker Compose config borrows ideas and code from:

* [https://github.com/breerly/compose-mesos](https://github.com/breerly/compose-mesos)
* [https://github.com/bobrik/mesos-compose](https://github.com/bobrik/mesos-compose)

## Versions

* Mesos Master: 0.26.0-0.2.145
* Mesos Slave: 0.26.0-0.2.145
* Marathon: Latest

## Special instructions for Mac OS X users

Start the Docker host VM and then get the IP of the Docker host

```
$ docker-machine start default
$ docker-machine env default
$ eval "$(docker-machine env default)"
```

You should see in the output something similar to this:

```
export DOCKER_HOST="tcp://192.168.99.100:2376"
```

Update the **hosts** file:

```
$ sudo nano /etc/hosts
```

Add the following line (adjust to the IP on your machine based on the DOCKER_HOST)

```
192.168.99.100  localdocker
```

Save and exit the file.

## Usage

Run the Mesos cluster:

```
$ docker-compose up -d
```

Load the following URLs in a browser:

* Mesos Master UI: [http://localdocker:5050/](http://localdocker:5050/)
* Marathon UI: [http://localdocker:8080/](http://localdocker:8080/)

To stop the cluster:

```
$ docker-compose stop
```

## Deploying a test app via the Marathon API:

```
$ curl -X POST -H "Accept: application/json" -H "Content-Type: application/json" localdocker:8080/v2/apps -d \
'{"id": "hello-test","cpus": 0.1,"mem": 32,"cmd": "echo hello; sleep 5", "instances": 1}'
```

## Deploying a Docker container via the Marathon API:

```
curl -X POST -H "Accept: application/json" -H "Content-Type: application/json" localdocker:8080/v2/apps -d ' \
{
    "id": "inky",
    "container": {
        "docker": {
            "image": "mesosphere/inky"
        },
        "type": "DOCKER",
        "volumes": []
    },
    "args": ["hello"],
    "cpus": 0.2,
    "mem": 32.0,
    "instances": 1
}'
```
