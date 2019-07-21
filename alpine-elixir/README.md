# Elixir on Alpine Linux

These dockerfiles provide a full installation of Erlang and Elixir on Alpine, intended for building releases.

## Usage

This image sets up a `default` user, with home set to `/opt/app` and owned by that user. The working directory
is also set to `$HOME`.

### Build 

To build an image use the included Makefile

```
# build the base elixir image
$ make build

# to build and push the erlang image
$ make build IMAGE=erlang
$ make release IMAGE=erlang

# build the builder-npm image 
$ make build IMAGE=elixir-builder-npm
```

### Versions

Versions for Erlang/OTP, Elixir and Alpine are defined in [VERSIONS](./VERSIONS) and pinned using sha256 digests.


### Run

To boot straight to a prompt in the image:

```
$ make iex
Erlang/OTP 21 [erts-10.3] [source] [64-bit] [smp:2:2] [ds:2:2:10] [async-threads:1] [hipe]

Interactive Elixir (1.9.1) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)>
```

### Push

To make your build available you need to push it to a repository

```
make release
make release IMAGE=erlang
```

## License

MIT
