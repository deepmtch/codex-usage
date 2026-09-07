# codex-usage

Show [Codex CLI](https://github.com/openai/codex) rate-limit usage for **every one of your
accounts in a single command**.

Codex stores exactly one login in `~/.codex/auth.json` and has no built-in account switcher,
so checking several accounts normally means logging out and back in for each one. `codex-usage`
gives each account its own `CODEX_HOME` directory and queries them all in parallel.

```
$ codex-usage

default  you@example.com       prolite  · 3 reset credit(s)
  codex                 weekly  ············    2%   resets in 6d 23h

work     you@work.com          plus     · 3 reset credit(s)
  codex                     5h  ████████████  100%   resets in 2h 9m
                        weekly  ███████·····   58%   resets in 5d 10h
```

## How it works

It speaks JSON-RPC to `codex app-server` and calls `account/read` and
`account/rateLimits/read` — the same data source the Codex TUI's `/usage` command renders.

Credentials are never touched: Codex handles auth and token refresh internally, and nothing
is parsed out of `auth.json`.

## Install

Requires `codex` on your `PATH` and Python 3.9+.

```bash
git clone <this-repo> ~/src/codex-usage
ln -s ~/src/codex-usage/codex-usage ~/.local/bin/codex-usage
```

## Usage

```bash
codex-usage              # all accounts, main quota bucket
codex-usage -v           # every limit bucket, including per-model ones
codex-usage --json       # machine-readable, for status bars
codex-usage -a work      # just one account (repeatable)

codex-usage add work     # log in a new account under its own CODEX_HOME
codex-usage list         # accounts and their CODEX_HOME paths
codex-usage run work     # launch codex as that account
codex-usage remove work  # delete an account (asks first)
```

Bars turn yellow at 50%, red at 75%, bright red at 90%.

## Notes

Not every plan reports every window. A `plus` account typically returns both a 5-hour and a
weekly window, while some plans return weekly only — `codex-usage` shows whatever the backend
actually reports rather than inventing rows.

Each account gets a fully independent `CODEX_HOME`, so accounts have separate config and
session history too, not just separate logins.

## Environment

| Variable | Default | Purpose |
| --- | --- | --- |
| `CODEX_ACCOUNTS_DIR` | `~/.codex-accounts` | Where extra accounts live |
| `CODEX_DEFAULT_HOME` | `~/.codex` | The default account |
| `CODEX_USAGE_TIMEOUT` | `25` | Per-account timeout, seconds |
| `NO_COLOR` | unset | Disable colour |

## License

MIT
