# reset postgres data

- `launchctl -w unload homebrew.mxcl.postgresql`
- `rm -rf /usr/local/var/postgres`
- `pg_ctl -D /usr/local/var/postgres initdb`
