# Electroneum Node via Docker

The [official electroneum](https://github.com/electroneum/electroneum) repo mentions instructions on building a docker image from their codebase, but don't provide you with a pre-built docker image.

So here it is.

- Based on Ubuntu image
- Using s6-overlay for process management
- Builds daemon and wallet-cli from source

## Up and running

With docker-compose:

```yml
version: "2"
services:
  app:
    image: jc21/electroneumd
    ports:
      - 26967:26967
      - 26968:26968
    volumes:
      - "./data/node:/root/.electroneum"
      - "./data/wallet:/wallet"
```

With docker vanilla:

```bash
docker run --detach \
    --name electroneumd \
    -p 26967:26967 \
    -p 26968:26968 \
    -v /path/to/blockchain:/root/.electroneum \
    -v /path/to/wallet:/wallet \
    jc21/electroneumd
```

## Daemon variables

Using environment variables passed to the container you can override some defaults for the daemon:

| Variable            | Default       |
| ------------------- | ------------- |
| `LOG_LEVEL`         | `0`           |
| `P2P_BIND_IP`       | `0.0.0.0`     |
| `P2P_BIND_PORT`     | `26967`       |
| `RPC_BIND_IP`       | `127.0.0.1`   |
| `RPC_BIND_PORT`     | `26968`       |
| `DAEMON_EXTRA_ARGS` |               |

You would specify them like so:

```yml
version: "2"
services:
  app:
    image: jc21/electroneumd
    ports:
      - 26967:26967
      - 26968:26968
    environment:
      - RPC_BIND_IP=0.0.0.0
      - DAEMON_EXTRA_ARGS=--confirm-external-bind
    volumes:
      - "./data/node:/root/.electroneum"
      - "./data/wallet:/wallet"
```

Or:

```bash
docker run --detach \
    --name electroneumd \
    -p 26967:26967 \
    -p 26968:26968 \
    -e RPC_BIND_IP=0.0.0.0 \
    -e DAEMON_EXTRA_ARGS=--confirm-external-bind \
    -v /path/to/blockchain:/root/.electroneum \
    -v /path/to/wallet:/wallet \
    jc21/electroneumd
```

`DAEMON_EXTRA_ARGS` is for advanced users who might want to get very specific with the daemon arguments.

**Important Note:**

If you are specifying a non-local `RPC_BIND_IP` then you **must** specify `--confirm-external-bind` as extra arguments.
