# NFSMW online client

Standalone build of the NFSMW online/LAN plugin.

## Layout

- `src/online.cpp` - DLL entrypoint
- `src/config.cpp` - config parsing and endpoint defaults
- `src/winsock_hooks.cpp` - TCP/UDP redirect hooks
- `src/internal_hooks.cpp` - MW internal LAN/online hooks
- `src/lan_select.cpp` - LAN server-list helpers
- `configs/ONLINE.cfg` - local test config
- `dist/` - build output
- `build.sh` - compiles all `src/*.cpp` files into `dist/online.asi`

The config supports endpoint overrides and logging.

Primary host options:

- `server_host`
- `bootstrap_host`
- `lobby_host`
- `race_host`
- `control_host`
- `control_alias_host`
- `lan_host`

Primary port options:

- `bootstrap_port`
- `lobby_port`
- `race_port`
- `control_port`
- `control_alias_port`
- `lan_port`

LAN and logging options:

- `online_override_host`
- `lan_override_host`
- `lan_provider_seed`
- `lan_discovery_host_id`
- `lan_discovery_name`
- `lan_discovery_port`
- `debug`

LAN through online:

```ini
lan_override_host = on
server_host = 203.0.113.10
lan_port = 9900
lan_provider_seed = on
```

With `lan_override_host=on`, the game's original LAN TCP connection is redirected to
the configured LAN endpoint. With `lan_provider_seed=on`, LAN broadcast discovery
packets are mirrored directly to `lan_host`; this is the first MW-safe discovery
hook while the exact in-memory server-list provider is still being mapped.

With `online_override_host=on`, the game's stock online/bootstrap endpoint is
redirected to `bootstrap_host`/`bootstrap_port`, falling back to `server_host`.

If a per-service host is omitted, it falls back to `server_host`. If `server_host` is
also omitted, the built-in default is used. Ports do not inherit from a main port;
omitted ports use their built-in defaults.

For same-machine or same-LAN race testing, keep `race_port` aligned with the
server's `RACE_ENDPOINT` and `RACE_LISTEN` UDP port. The client can consume the
server-advertised race endpoint, but the advertised port still has to be a port
where the server is actually listening.

The game also uses local UDP `3658` for race peer traffic. Keep `3658/udp` free
on each client machine, especially when testing multiple clients locally. This is
separate from `race_port`, which points at the relay.

This build focuses on endpoint redirection, LAN server injection, and logging.

## Build

```sh
./build.sh
```

The build produces one file:

- `dist/online.asi`
