Nakama Project Template
===

> An example project template on how to set up and write custom logic in Nakama server.

The codebase shows a few simple gameplay features written in all three of the runtime framework languages supported by the server: Go, Lua, and TypeScript. The code shows how to read/write storage objects, send in-app notifications, parse JSON, update player wallets, and handle errors.

For more documentation have a look at:

* https://heroiclabs.com/docs/nakama/server-framework/introduction/index.html
* https://heroiclabs.com/docs/nakama/concepts/storage/
* https://heroiclabs.com/docs/nakama/concepts/user-accounts/#virtual-wallet
* https://heroiclabs.com/docs/nakama/concepts/notifications/
* https://heroiclabs.com/docs/nakama/concepts/multiplayer/authoritative/

For a detailed guide on setting up TypeScript check out the [Setup page](https://heroiclabs.com/docs/nakama/server-framework/typescript-runtime/).

__NOTE__ You can remove the Go, Lua or TypeScript code within this project to develop with just the single language you prefer.

### Prerequisites

The codebase requires these development tools:

* Go compiler and runtime: 1.15.2 or greater.
* Docker Engine: 19.0.0 or greater.
* Basic UNIX tools or knowledge on the Windows equivalents.

### Go Dependencies

The project uses Go modules which should be vendored as normal:

```shell
env GO111MODULE=on GOPRIVATE="github.com" go mod vendor
```

### Start

The recommended workflow is to use Docker and the compose file to build and run the game server, database resources and tensorflow-serving (AI model server).

```shell
docker compose up --build nakama
```

### Recompile / Run

When the containers have been started as shown above you can replace just the game server custom code and recompile it with the `-d` option.

```shell
docker compose up -d --build nakama
```

### Stop

To stop all running containers you can use the Docker compose sub-command.

```shell
docker compose down
```

You can wipe the database and workspace with `docker compose down -v` to remove the disk volumes.

### Run RPC function

A bunch of RPC IDs are registered with the server logic. A couple of these are:

* "rewards" in Go or as "reward" in Lua.
* "refreshes" in Go or as "refresh" in Lua.

To execute the RPC function with cURL generated a session token:

```shell
curl "127.0.0.1:7350/v2/account/authenticate/device" --data "{\"id\": \""$(uuidgen)"\"}" --user 'defaultkey:'
```

Take the session token in the response and use it to execute the RPC function as the user:

```shell
curl "127.0.0.1:7350/v2/rpc/rewards" -H 'Authorization: Bearer $TOKEN' --data '""'
```

This will generate an RPC response on the initial response in that day and grant no more until the rollover.

```
{"payload":"{\"coins_received\":500}"}
or
{"payload":"{\"coins_received\":0}"}
```

You can also skip the cURL steps and use the [Nakama Console's API Explorer](http://127.0.0.1:7351/apiexplorer) to execute the RPCs.