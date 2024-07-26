# WireGuard Easy

[![Build & Publish Docker Image to Docker Hub](https://github.com/wg-easy/wg-easy/actions/workflows/deploy.yml/badge.svg?branch=production)](https://github.com/wg-easy/wg-easy/actions/workflows/deploy.yml)
[![Lint](https://github.com/wg-easy/wg-easy/actions/workflows/lint.yml/badge.svg?branch=master)](https://github.com/wg-easy/wg-easy/actions/workflows/lint.yml)
![Docker](https://img.shields.io/docker/pulls/weejewel/wg-easy.svg)
[![Sponsor](https://img.shields.io/github/sponsors/weejewel)](https://github.com/sponsors/WeeJeWel)
![GitHub Stars](https://img.shields.io/github/stars/wg-easy/wg-easy)

You have found the easiest way to install & manage WireGuard on any Linux host!

<p align="center">
  <img src="./assets/screenshot.png" width="802" />
</p>

## Features
[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/ByxE6zxeyLY&t/0.jpg)](https://www.youtube.com/watch?v=ByxE6zxeyLY&t)


[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/W9hA31FuQSY/0.jpg)](https://www.youtube.com/watch?v=W9hA31FuQSY)

* 一体化：WireGuard + Web UI。
*  安装简便，使用简单。
* 列出、创建、编辑、删除、启用和禁用客户端。
* 显示客户的二维码。
* 下载客户端的配置文件。
* 有关已连接客户端的统计信息。
* 每个连接客户端的 Tx/Rx 图表。
* Gravatar 支持。
* 自动亮/暗模式
* 多语言支持
* UI_TRAFFIC_STATS（默认关闭）

## Requirements

* 具有支持 WireGuard 内核的主机（所有现代内核）。
*安装了 Docker 的主机。

## Versions

提供多个 docker 镜像供您获取，这将帮助您决定哪一个最适合您。

| tag | Branch | Example | Description |
| - | - | - | - |
| `latest` | production | `ghcr.io/wg-easy/wg-easy:latest` or `ghcr.io/wg-easy/wg-easy` | stable as possbile get bug fixes quickly when needed, deployed against `production`. |
| `13` | production | `ghcr.io/wg-easy/wg-easy:13` | same as latest, stick to a version tag. |
| `nightly` | master | `ghcr.io/wg-easy/wg-easy:nightly` | mostly unstable gets frequent package and code updates, deployed against `master`. |
| `development` | pull requests | `ghcr.io/wg-easy/wg-easy:development` | used for development, testing code from PRs before landing into `master`. |

## Installation

### 1. Install Docker

如果你尚未安装 Docker，请运行以下命令进行安装：

```bash
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker $(whoami)
exit
```

And log in again.

### 2. 轻松运行 WireGuard

要自动安装并运行 wg-easy，只需运行：

```
  docker run -d \
  --name=wg-easy \
  -e LANG=en \
  -e WG_HOST=<🚨YOUR_SERVER_IP> \
  -e PASSWORD_HASH=<🚨YOUR_ADMIN_PASSWORD_HASH> \
  -e PORT=31821 \
  -e WG_PORT=31820 \
  -v ~/.wg-easy:/etc/wireguard \
  -p 31820:31820/udp \
  -p 31821:31821/tcp \
  --cap-add=NET_ADMIN \
  --cap-add=SYS_MODULE \
  --sysctl="net.ipv4.conf.all.src_valid_mark=1" \
  --sysctl="net.ipv4.ip_forward=1" \
  --restart unless-stopped \
  ghcr.io/wg-easy/wg-easy
```

> 💡 Replace `YOUR_SERVER_IP` with your WAN IP, or a Dynamic DNS hostname.
>
> 💡 Replace `YOUR_ADMIN_PASSWORD_HASH` 使用 bcrypt 密码哈希值替换以登录 Web UI. See [How_to_generate_an_bcrypt_hash.md](./How_to_generate_an_bcrypt_hash.md) for know how generate the hash.

The Web UI will now be available on `http://0.0.0.0:51821`.

> 💡 Your configuration files will be saved in `~/.wg-easy`

WireGuard Easy can be launched with Docker Compose as well - just download
[`docker-compose.yml`](docker-compose.yml), make necessary adjustments and
execute `docker compose up --detach`.

### 3. Sponsor

Are you enjoying this project? [Buy Emile a beer!](https://github.com/sponsors/WeeJeWel) 🍻

## Options

可以通过在命令中使用设置环境变量来配置这些选项 `-e KEY="VALUE"` in the `docker run` command.

| Env | Default | Example | Description                                                                                                                                          |
| - | - | - |------------------------------------------------------------------------------------------------------------------------------------------------------|
| `PORT` | `51821` | `6789` | TCP port for Web UI.                                                                                                                                 |
| `WEBUI_HOST` | `0.0.0.0` | `localhost` | IP address web UI binds to.                                                                                                                          |
| `PASSWORD_HASH` | - | `$2y$05$Ci...` | When set, requires a password when logging in to the Web UI. See [How to generate an bcrypt hash.md]("https://github.com/wg-easy/wg-easy/blob/master/How_to_generate_an_bcrypt_hash.md") for know how generate the hash. |
| `PASSWORD` (deprecated) | - | `foobar123` | When set, requires a password when logging in to the Web UI. *(Not used if `PASSWORD_HASH` is set)*                                                  |
| `WG_HOST` | - | `vpn.myserver.com` | The public hostname of your VPN server.                                                                                                              |
| `WG_DEVICE` | `eth0` | `ens6f0` | Ethernet device the wireguard traffic should be forwarded through.                                                                                   |
| `WG_PORT` | `51820` | `12345` | The public UDP port of your VPN server. WireGuard will listen on that (othwise default) inside the Docker container.                                 |
| `WG_CONFIG_PORT`| `51820` | `12345` | The UDP port used on [Home Assistant Plugin](https://github.com/adriy-be/homeassistant-addons-jdeath/tree/main/wgeasy)                               
| `WG_MTU` | `null` | `1420` | The MTU the clients will use. Server uses default WG MTU.                                                                                            |
| `WG_PERSISTENT_KEEPALIVE` | `0` | `25` | Value in seconds to keep the "connection" open. If this value is 0, then connections won't be kept alive.                                            |
| `WG_DEFAULT_ADDRESS` | `10.8.0.x` | `10.6.0.x` | Clients IP address range.                                                                                                                            |
| `WG_DEFAULT_DNS` | `1.1.1.1` | `8.8.8.8, 8.8.4.4` | DNS server clients will use. If set to blank value, clients will not use any DNS.                                                                    |
| `WG_ALLOWED_IPS` | `0.0.0.0/0, ::/0` | `192.168.15.0/24, 10.0.1.0/24` | Allowed IPs clients will use.                                                                                                                        |
| `WG_PRE_UP` | `...` | - | See [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L19) for the default value.                                             |
| `WG_POST_UP` | `...` | `iptables ...` | See [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L20) for the default value.                                             |
| `WG_PRE_DOWN` | `...` | - | See [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L27) for the default value.                                             |
| `WG_POST_DOWN` | `...` | `iptables ...` | See [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L28) for the default value.                                             |
| `LANG` | `en` | `de` | Web UI language (Supports: en, ua, ru, tr, no, pl, fr, de, ca, es, ko, vi, nl, is, pt, chs, cht, it, th, hi).                                        |
| `UI_TRAFFIC_STATS` | `false` | `true` | Enable detailed RX / TX client stats in Web UI                                                                                                       |
| `UI_CHART_TYPE` | `0` | `1` | UI_CHART_TYPE=0 # Charts disabled, UI_CHART_TYPE=1 # Line chart, UI_CHART_TYPE=2 # Area chart, UI_CHART_TYPE=3 # Bar chart                           |

> If you change `WG_PORT`, make sure to also change the exposed port.

## 更新

要更新到最新版本，只需运行：

```bash
docker stop wg-easy
docker rm wg-easy
docker pull ghcr.io/wg-easy/wg-easy
```

And then run the `docker run -d \ ...` 再次运行上述命令。

使用 Docker Compose，WireGuard Easy 可以通过单个命令进行更新：（ docker compose up --detach --pull always如果在 Compose 文件中指定了图像标签但不是latest，请确保将其更改为所需的标签；默认情况下，它会被省略并 默认为latest）。
如果拉取了较新的图像，WireGuared Easy 容器将自动重新创建。

With Docker Compose WireGuard Easy can be updated with a single command:
`docker compose up --detach --pull always` (if an image tag is specified in the
Compose file and it is not `latest`, make sure that it is changed to the desired
one; by default it is omitted and
[defaults to `latest`](https://docs.docker.com/engine/reference/run/#image-references)). \
The WireGuared Easy container will be automatically recreated if a newer image
was pulled.

## Common Use Cases

* [Using WireGuard-Easy with Pi-Hole](https://github.com/wg-easy/wg-easy/wiki/Using-WireGuard-Easy-with-Pi-Hole)
* [Using WireGuard-Easy with nginx/SSL](https://github.com/wg-easy/wg-easy/wiki/Using-WireGuard-Easy-with-nginx-SSL)

For less common or specific edge-case scenarios, please refer to the detailed information provided in the [Wiki](https://github.com/wg-easy/wg-easy/wiki).
