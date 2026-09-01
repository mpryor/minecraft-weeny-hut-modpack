# Weeny Hut modpacks

The packwiz modpacks for the Weeny Hut Minecraft servers, published to
**https://pack.weenyhut.com**.

That address is the contract. Both servers fetch it when their container starts,
and every player's Prism instance fetches it on launch. It is a CNAME onto GitHub
Pages, so this repo can be renamed or replaced without anyone re-pointing an
instance — which is why the packs live behind a domain rather than a
`github.io/<repo>` URL.

## Layout

| path | what it is |
|---|---|
| `modpack/<env>/` | the packwiz pack — this directory is what Pages serves |
| `modpack/local-mods/` | mods not on Modrinth/CurseForge, hosted here |
| `catalog/<env>.toml` | why each mod is in the pack; CI checks it against the pack |
| `client-extras/<env>/` | client-only files (shaderpacks) folded into the generic pack |
| `server-config/<env>/` | server-side mod config, shipped inside the generic pack |
| `scripts/` | pack tooling — see below |

Pages publishes `modpack/`, so `modpack/neoforge-prod/pack.toml` is served at
`https://pack.weenyhut.com/neoforge-prod/pack.toml`.

## Environments

`neoforge-dev` and `neoforge-prod` are the live packs. `dev` and `prod` are the
older Fabric line, kept because their URLs are still referenced.

Promotion copies dev to prod; it never changes a URL. Prod picks the new pack up
on its next container start, which — since autoshutdown stops the server when it
empties — is the next `/mcstart`.

## CI

`publish-modpack.yml` verifies then publishes. The two gates:

- `packwiz refresh` must be a no-op — a stale `index.toml` hash means clients and
  the server silently install the wrong version, with no error
- `check-catalog.py` must match every pack

Only `master` deploys. `promote-modpack.yml` moves dev to prod.

## Scripts

| script | use |
|---|---|
| `build-client-extras.py` | builds the AutoModpack generic pack (client extras + server config) |
| `check-catalog.py` | the catalog gate |
| `promote-modpack.sh` | dev to prod |
| `gen-sdlink-config.sh`, `GenSdlinkConfig.java` | regenerate the Simple Discord Link config from mod defaults |

## Related

Infrastructure — ECS, the servers, DNS, and `pack.weenyhut.com` itself — lives in
`minecraft-weeny-hut-terraform`, along with `scripts/redeploy` and the
`autoshutdown` script. This repo was split out of
`minecraft-weeny-hut-server`, which held the retired CloudFormation stacks; the
history here is that repo's.
