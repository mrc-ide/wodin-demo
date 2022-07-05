# WODIN demo

## Docker

Spin up the [odin.api](https://github.com/mrc-ide/odin.api) server on a docker private network, then bring up [wodin](https://github.com/mrc-ide/wodin) pointing at our configuration directory

```
docker network create wodin_nw
docker run -d --name odin.api --rm \
    --network=wodin_nw \
    mrcide/odin.api:main
docker run -d --name wodin --rm \
    -p 3000:3000 --network=wodin_nw \
    -v $PWD/config:/config:ro \
    mrcide/wodin:4407379 /config
```

The demo app will be available at http://localhost:3000

You can replace branch names on both components as required, but they must be compatible with one another.

In a production setting we would not expose port 3000 at all, but add a proxy to this network and expose everything over https.

## On metal

Bring up odin.api much as before, but this time we expose the port to the host

```
docker run -d --name odin.api --rm \
    -p 8001:8001
    mrcide/odin.api:main
```

Edit the config/wodin.config.json to change the url of `odinAPI` to be `https://127.0.0.1:8001`

Run

```
npm install
run run serve
```

The demo app will be available at http://localhost:3000
