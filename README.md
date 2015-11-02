# YouTrack on Docker

Based on YouTrack 6.5.16853 (October, 30 2015)

This repository contains a Docker image of JetBrains [YouTrack](http://www.jetbrains.com/youtrack).

* The Docker image is available at [tonidy/youtrack-docker](https://registry.hub.docker.com/u/tonidy/youtrack-docker)
* The GitHub repository is available at [tonidy/youtrack-docker](https://github.com/tonidy/youtrack-docker)
* This project based on [uniplug/youtrack](https://registry.hub.docker.com/u/uniplug/youtrack)

## Usage

First, pull the Docker image using the following command:

```bash
docker pull tonidy/youtrack-docker
```

Next, create a container.

```bash
docker run -t tonidy/youtrack-docker
```

YouTrack starts and listens on port 80 in the container. To map it to the host's port 80, use the following command to create the container instead:

```bash
docker run --name="youtrack" -p 80:80 -t tonidy/youtrack-docker
```

### Additional settings

YouTrack stores its data and backups at ```/opt/youtrack/data/``` and ```/opt/youtrack/backup/``` in the container. If you wish to re-use data, it is a good idea to set up a volume mapping for these two paths. For example:

```bash
docker run -t \
 --name="youtrack" \
 -v /data/youtrack/data/:/opt/youtrack/data/ \
 -v /data/youtrack/backup/:/opt/youtrack/backup/ \
 -p 80:80 \
 tonidy/youtrack-docker
```

### Service example

```ini
[Unit]
Description=YouTrack
After=docker.service nginx-proxy.service
Requires=docker.service nginx-proxy.service

[Service]
KillMode=none
ExecStartPre=-/usr/bin/docker kill youtrack
ExecStartPre=-/usr/bin/docker rm youtrack
ExecStart=/usr/bin/docker run -t \
          --name youtrack \
          -v /data/youtrack/data/:/opt/youtrack/data/ \
          -v /data/youtrack/backup/:/opt/youtrack/backup/ \
          -e VIRTUAL_HOST=youtrack.example.com \
          uniplug/youtrack
ExecStop=-/usr/bin/docker stop youtrack
```
