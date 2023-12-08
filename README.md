# [![logo](https://avatars.githubusercontent.com/u/223312?s=48&v=4)](https://transmissionbt.com/) Transmission

A docker container for Transmission built from source https://github.com/transmission/transmission/releases

What is Transmission? Transmission is a cross-platform BitTorrent client with a simple web interface.

# How to use this image

## Run the latest version (3.00)

    docker run -d -p 9091:9091 hdorio/transmission

## Run a specific version (2.84)

    docker run -d -p 9091:9091 hdorio/transmission:2.84

## Configuration

ENVIROMENT VARIABLES (only effective on the first `docker run`)

all settings are available through env variables, you need to replace every dash by an underscore
example for rpc-authentication-required, rpc-whitelist-enabled, rpc-username and rpc-password

    docker run -d -p 9091:9091 \
      -e RPC_AUTHENTICATION_REQUIRED=true \
      -e RPC_PASSWORD=password \
      -e RPC_USERNAME=user \
      -e RPC_WHITELIST_ENABLED=false \
      hdorio/transmission


## Folders

Default locations for folders and files are:

  * `Downloads` - `/home/transmission/Downloads/`
  * `Settings` - `/home/transmission/.config/transmission-daemon/settings.json`
  * `Torrents` - `/home/transmission/.config/transmission-daemon/torrents/`

## Volumes

Two volumes are set for:

  * `Downloads` - `/home/transmission/Downloads/`
  * `Settings` - `/home/transmission/.config/transmission-daemon/`


but you can export downloads if you want

    mkdir downloads
    docker run -d -p 9091:9091 -v $(pwd)/downloads:/home/transmission/Downloads hdorio/transmission

# User Feedback

## Issues

If you have any problems with or questions about this image, please contact me
through a [GitHub issue](https://github.com/hdorio/docker-transmission/issues).
