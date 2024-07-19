# CUPS-docker

Run a CUPS print server on a remote machine to share USB printers over WiFi. Built primarily to use with Raspberry Pis as a headless server, but there is no reason this wouldn't work on `amd64` machines. Tested and confirmed working on a Raspberry Pi 3B+ (`arm/v7`) and Raspberry Pi 4 (`arm64/v8`).

Container packages available from Docker Hub and Github Container Registry (ghcr.io)
  - Docker Hub Image: `tartarismo/cups-docker`
  - GHCR Image: `ghcr.io/tartarismo/cups-docker`

## Usage
Quick start with default parameters
```sh
docker run -d -p 631:631 --device /dev/bus/usb --name cups tartarismo/cups-docker
```

Customizing your container
```sh
docker run -d --name cups \
    --restart unless-stopped \
    -p 631:631 \
    --device /dev/bus/usb \
    -e CUPSADMIN=admin \
    -e CUPSPASSWORD=password \
    -e TZ="Europe/Rome" \
    -v <persistent-config-folder>:/etc/cups \
    tartarismo/cups-docker
```
> Note: :P make sure you use valid TZ string. Also changing the default username and password is highly recommended.

### Parameters and defaults
- `port` -> default cups network port `631:631`. Change not recommended unless you know what you're doing
- `device` -> used to give docker access to USB printer. Default passes the whole USB bus `/dev/bus/usb`, in case you change the USB port on your device later. change to specific USB port if it will always be fixed, for eg. `/dev/bus/usb/001/005`.

#### Optional parameters
- `name` -> whatever you want to call your docker image. using `cups` in the example above.
- `volume` -> adds a persistent volume for CUPS config files if you need to migrate or start a new container with the same settings

Environment variables that can be changed to suit your needs, use the `-e` tag
| # | Parameter    | Default            | Type   | Description                       |
| - | ------------ | ------------------ | ------ | --------------------------------- |
| 1 | TZ           | "Europe/Rome" | string | Time zone of your server          |
| 2 | CUPSADMIN    | admin              | string | Name of the admin user for server |
| 3 | CUPSPASSWORD | password           | string | Password for server admin         |

### docker-compose
```yaml
version: "3"
services:
    cups:
        image: tartarismo/cups-docker
        container_name: cups
        restart: unless-stopped
        ports:
            - "631:631"
        devices:
            - /dev/bus/usb:/dev/bus/usb
        environment:
            - CUPSADMIN=admin
            - CUPSPASSWORD=password
            - TZ="Europe/Rome"
        volumes:
            - <persistent-config-path>:/etc/cups
```

## Server Administration
You should now be able to access CUPS admin server using the IP address of your headless computer/server http://192.168.xxx.xxx:631, or whatever. If your server has avahi-daemon/mdns running you can use the hostname, http://printer.local:631. (IP and hostname will vary, these are just examples)

If you are running this on your PC, i.e. not on a headless server, you should be able to log in on http://localhost:631

## Thanks
Based on the work done by **anujdatar**: [https://github.com/anujdatar/cups-docker](https://github.com/anujdatar/cups-docker)
