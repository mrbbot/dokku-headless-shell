# dokku headless shell

A headless shell service for dokku created expressly for the Go [`chromedp` package](https://github.com/chromedp/chromedp).

## Requirements

- dokku 0.8.1+
- docker 1.8.x

## Installation

```shell
# on 0.4.x+
sudo dokku plugin:install https://github.com/mrbbot/dokku-headless-shell.git headlessshell
```

## Commands

```
headlessshell:app-links <app>          List all headless shell service links for a given app
headlessshell:create <name>            Create a headless shell service with environment variables
headlessshell:destroy <name>           Delete the service, delete the data and stop its container if there are no links left
headlessshell:enter <name> [command]   Enter or run a command in a running headless shell service container
headlessshell:exists <service>         Check if the headless shell service exists
headlessshell:expose <name> [port]     Expose a headless shell service on custom port if provided (random port otherwise)
headlessshell:info <name>              Print the connection information
headlessshell:link <name> <app>        Link the headless shell service to the app
headlessshell:linked <name> <app>      Check if the headless shell service is linked to an app
headlessshell:list                     List all headless shell services
headlessshell:logs <name> [-t]         Print the most recent log(s) for this service
headlessshell:promote <name> <app>     Promote service <name> as HEADLESS_SHELL_URL in <app>
headlessshell:restart <name>           Graceful shutdown and restart of the headless shell service container
headlessshell:start <name>             Start a previously stopped headless shell service
headlessshell:stop <name>              Stop a running headless shell service
headlessshell:unexpose <name>          Unexpose a previously exposed headless shell service
headlessshell:unlink <name> <app>      Unlink the headless shell service from the app
headlessshell:upgrade <name>           Upgrade service <service> to the specified version
```

## Rsage

```shell
# Create a headless shell service named lolipop
dokku headless shell:create lolipop

# You can also specify the image and image
# version to use for the service
export HEADLESS_SHELL_IMAGE="chromedp/headless-shell"
export HEADLESS_SHELL_IMAGE_VERSION="latest"
dokku headlessshell:create lolipop

# Get connection information as follows
dokku headlessshell:info lolipop

# You can also retrieve a specific piece of service info via flags
dokku headlessshell:info lolipop --config-dir
dokku headlessshell:info lolipop --data-dir
dokku headlessshell:info lolipop --dsn
dokku headlessshell:info lolipop --exposed-ports
dokku headlessshell:info lolipop --id
dokku headlessshell:info lolipop --internal-ip
dokku headlessshell:info lolipop --links
dokku headlessshell:info lolipop --service-root
dokku headlessshell:info lolipop --status
dokku headlessshell:info lolipop --version

# A bash prompt can be opened against a running service
# filesystem changes will not be saved to disk
dokku headlessshell:enter lolipop

# You may also run a command directly against the service
# filesystem changes will not be saved to disk
dokku headlessshell:enter lolipop ls -lah /

# A headless shell service can be linked to a
# container this will use native docker
# links via the docker-options plugin
# here we link it to our 'playground' app
# NOTE: this will restart your app
dokku headlessshell:link lolipop playground

# The following environment variables will be set automatically by docker (not
# on the app itself, so they won’t be listed when calling dokku config)
#
#   DOKKU_HEADLESS_SHELL_LOLIPOP_NAME=/random_name/HEADLESS_SHELL
#   DOKKU_HEADLESS_SHELL_LOLIPOP_PORT=tcp://172.17.0.1:9222
#   DOKKU_HEADLESS_SHELL_LOLIPOP_PORT_9222_TCP=tcp://172.17.0.1:9222
#   DOKKU_HEADLESS_SHELL_LOLIPOP_PORT_9222_TCP_PROTO=tcp
#   DOKKU_HEADLESS_SHELL_LOLIPOP_PORT_9222_TCP_PORT=9222
#   DOKKU_HEADLESS_SHELL_LOLIPOP_PORT_9222_TCP_ADDR=172.17.0.1
#
# and the following will be set on the linked application by default
#
#   HEADLESS_SHELL_URL=ws://dokku-headlessshell-lolipop:9222
#
# NOTE: the host exposed here only works internally in docker containers. If
# you want your container to be reachable from outside, you should use `expose`.

# Another service can be linked to your app
dokku headlessshell:link other_service playground

# Since HEADLESS_SHELL_URL is already in use, another environment variable will be
# generated automatically
#
#   DOKKU_HEADLESS_SHELL_BLUE_URL=ws://dokku-headlessshell-other-service:9222

# You can then promote the new service to be the primary one
# NOTE: this will restart your app
dokku headlessshell:promote other_service playground

# This will replace HEADLESS_SHELL_URL with the url from other_service and generate
# another environment variable to hold the previous value if necessary.
# you could end up with the following for example:
#
#   HEADLESS_SHELL_URL=ws://dokku-headlessshell-other-service:9222
#   DOKKU_HEADLESS_SHELL_BLUE_URL=ws://dokku-headlessshell-other-service:9222
#   DOKKU_HEADLESS_SHELL_SILVER_URL=ws://dokku-headlessshell-lolipop:9222

# You can also unlink a headless shell service
# NOTE: this will restart your app and unset related environment variables
dokku headlessshell:unlink lolipop playground

# You can tail logs for a particular service
dokku headlessshell:logs lolipop
dokku headlessshell:logs lolipop -t # to tail

# Finally, you can destroy the container
dokku headlessshell:destroy lolipop
```

## Disabling `docker pull` calls

If you wish to disable the `docker pull` calls that the plugin triggers, you may set the `HEADLESS_SHELL_DISABLE_PULL` environment variable to `true`. Once disabled, you will need to pull the service image you wish to deploy as shown in the `stderr` output.

Please ensure the proper images are in place when `docker pull` is disabled.

## Thanks

This plugin was extensively based on the the [lazyatom/dokku-chrome](https://github.com/lazyatom/dokku-chrome) plugin.
