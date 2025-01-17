## vaultbin

[![MIT](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)
[![Release CI](https://github.com/merlinfuchs/vaultbin/actions/workflows/release.yml/badge.svg)](https://github.com/merlinfuchs/vaultbin/releases)

Vaultbin is a blazingly fast and secure alternative to Pastebin and Hastebin.

For each paste Vaultbin generates a random 128 bit AES key and encrypts the paste with it. 
To identify the encrypted paste in the database it's using a hashed version of that key.
The encryption key is then used in the URL to request and decrypt the paste.  
This way nobody with access to the database can read a paste unless they have the exact URL to that paste.

Vaultbin compiles to a single binary and doesn't depend on an external database.
The binary includes everything you need to host an instance.

This project was initially inspired by [zer0b.in](https://github.com/zer0bin-dev/zer0bin).

## Public Instances

| URL                                            | Expiration | Max paste size | Location                            |
| ---------------------------------------------- | ---------- | -------------- | ----------------------------------- |
| [vaultb.in](https://vaultb.in)                 | 30 days    | 69,420 chars   | Germany                             |

## API Routes

| Route | Method | Description | Parameters
| --- | --- | --- | --- |
| `/pastes/{paste_id}` | `GET` | Get information about a paste | None
| `/api/pastes/{paste_id}/raw` | `GET` | Get the raw paste | None
| `/api/stats` | `GET` | Does nothing | None
| `/pastes` | `POST` | Create a paste | `language`, `content`, `expiration`

## Installation

### Prebuilt Binaries

You can find prebuilt binaries for the most common operating systems [here](https://github.com/merlinfuchs/vaultbin/releases).

### Build from source

To build this project from source you need [node](https://nodejs.org/en/download/) and [rust](https://www.rust-lang.org/tools/install) installed.

#### Build the frontend:
```shell
npm run install
npm run build
```

#### Build the backend:
```shell
# just build
cargo build --release
# or build and install it in PATH
cargo install --path .
```

The frontend code is embedded into the binary during the build process. 

## Configuration

Vaultbin will look for a `Config.toml` file in the directory where you start it.  
The default config looks like this:
```toml
host = "127.0.0.1" # host to bind on
port = 8080 # port to bind on

max_paste_size = 69420 # max size of pastes in bytes
max_expiration = 2592000 # (30 days) seconds after a paste will be deleted

[database]
path = "./data" # path where data is stored
cache_size = 100000000 # (100MB) size of the database cache (recently used pastes will be kept in memory if possible)

[ratelimit] # 5 request / 5 seconds
burst_size = 5 # number of request before subsequent request are block
per_second = 5 # seconds it takes to refill one request
reverse_proxy = false # if the backend is deployed behind a revers proxy -> this changes the way the peers IP is retrieved
```

In addition to creating a `Config.toml` file, you can also override these values using environment variables, for example:

```shell
VAULTBIN_HOST=0.0.0.0
VAULTBIN_DATABASE__CACHE_SIZE=9999999
```
