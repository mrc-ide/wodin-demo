# WODIN demo

## Docker

Spin up the [odin.api](https://github.com/mrc-ide/odin.api) server on a docker private network, then bring up [wodin](https://github.com/mrc-ide/wodin) pointing at our configuration directory

```
docker network create wodin_nw
docker run -d --pull=always --name odin.api --rm \
    --network=wodin_nw \
    mrcide/odin.api:main
docker run -d --pull=always --name wodin --rm \
    -p 3000:3000 --network=wodin_nw \
    -v $PWD/config:/config:ro \
    mrcide/wodin:main /config
```

The demo app will be available at http://localhost:3000

To use the builtin demo config, you can omit the `-v` and `/config` parts

```
docker run -d --pull=always --name wodin --rm \
    -p 3000:3000 --network=wodin_nw \
    mrcide/wodin:main
```

You can replace branch names on both components as required, but they must be compatible with one another (in terms of the code generated by odin.api and expected by wodin - `main`/`main` will always work, other combinations are possible).

In a production setting we would not expose port 3000 at all, but add a proxy to this network and expose everything over https.

## On metal

We can also run wodin on metal, without a docker container, with a few tweaks.

Bring up odin.api much as before, but this time we expose odin.api port to the host

```
docker run -d --name odin.api --rm \
    -p 8001:8001 \
    mrcide/odin.api:main
```

Edit the config/wodin.config.json to change the url of the `odinAPI` key to be `http://127.0.0.1:8001`

```json
{
    "courseTitle": "WODIN Example",
    "port": 3000,
    "appsPath": "apps",
    "odinAPI": "http://127.0.0.1:8001"
}
```

Then run

```
npm install
run run serve
```

The demo app will be available at http://localhost:3000

This requires that the npm package has been updated recently (not currently part of our workflows).
