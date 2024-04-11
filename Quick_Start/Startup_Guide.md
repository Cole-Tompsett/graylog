# Quick Start Guide
In the following document, we will go over the following critical steps required when setting up this tool. This includes the following: 
- Setting up the Timezone
- Setting up Docker Compose for Graylog
- Networking
- Creating a Password
- Figuring your Ports out
- Starting the tool properly through the containers


## Setting the Time
First we need to ensure that the time is accurate for all these logs that come in so we need to set the timezone to UTC
```bash
sudo timedatectl set-timezone UTC
```

# Docker Compose

Once you have read through the above information you are finally ready to boot up the Graylog container using the provided Docker-compose file. \
\
<b>BUT...</b>\
\
before you can properly use this file you will need to modify the "docker-compose.yml" file.

## Networking
The first thing you will need to change is the "parent", "subnet", and "gateway":
```yaml
driver_opts:
  parent: ens33
ipam:
  config:
  - subnet: 198.51.100.0/24
    gateway: 198.51.100.1
```
Then we will want to assign an IP address to each of the containers; mongo, opensearch, and graylog. 
```yaml
networks:
  graynet:
  grayworld:
    ipv4_address: 198.51.100.22
```
## Password
Next we will want to change the pre-made password used to login to the Graylog interface and the timezone used in Graylog.

First we will need to make a password by running the following command in the CLI
```sh
echo -n YourPassword | shasum -a 256
```
Next we can paste that shasum in to the password variable in the docker-compose file.
```yaml
GRAYLOG_ROOT_PASSWORD_SHA2: "5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8"
```
The current set password is "password"

## Ports

The final step that we will want to modify is the "ports" section of the docker-compose file.
```yaml
ports:
  - 9000:9000/tcp   # Graylog web interface and REST API
  - 1514:1514/tcp   # Syslog
  - 1514:1514/udp   # Syslog
  - 12201:12201/tcp # GELF
  - 12201:12201/udp # GELF
  - 5555:5555/tcp   # Zeek Conn
  - 5555:5555/udp   #Zeek Conn
```
The above are examples

You can open up as many ports as you would like but it is reccommended to limit the amount of open ports and only have ones open that are going to be used. 
It is also reccommended that you only have one prot dedicated to one stream of data. For example in the above code port 5555 is dedicated to only zeek connection logs.

## Starting the containers
<b>NOW THE TIME HAS COME...</b>

Now that you have all the required configurations made you can finally start the container by running the following command:
```sh
docker-compose build
docker-compose up -d
```

## Extra Resources
If you have any further questions do not hesitate to reach out to one of our contributors or consult the documentation provided in this repo.
If you wish to seek help setting up mailing watch this video: https://www.youtube.com/watch?v=DwYwrADwCmg
