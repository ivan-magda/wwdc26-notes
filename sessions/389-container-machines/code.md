# Code samples — Session 389

The "Code" tab for this session is the sequence of CLI invocations shown during the
demo. They are `container machine ...` subcommands of the open-source `container` tool,
plus a `swift run` to build/run the Vapor app. Timestamps map to the transcript.

## ~4:39 — Discover the subcommand

```console
container machine
```

Prints the overview of available actions (`create`, `run`, `stop`, `list`, etc.).

## ~4:56 — Create a default machine from an OCI image

```console
container machine create --name demo --set-default alpine
```

Creates a Container machine named `demo`, sets it as the default on the Mac (so later
commands can omit the name), starting from the `alpine` OCI image.

## ~5:34 — Run a one-off command

```console
container machine run echo hi
```

## ~5:48 — Confirm the runtime is Linux

```console
container machine run uname
```

On macOS `uname` prints `Darwin`; inside the machine it prints `Linux`.

## ~6:27 — Interactive shell

```console
container machine run
```

With no additional arguments, `run` starts an interactive session. Inside it, `whoami`
and `pwd` return the same username and path as the Mac (automatic user + working-directory
mirroring).

## ~7:51 — List machines

```console
container machine list
```

Displays the name, IP address, and resource information of all Container machines. (The
IP is copied for use as the Vapor server's hostname later.)

## ~9:15 — Build and run the Vapor app (inside the machine)

```console
swift run
```

Run from inside the interactive shell to compile and start the Vapor web server, which is
then reached from Safari on the Mac at `http://<machine-ip>:8080`.
