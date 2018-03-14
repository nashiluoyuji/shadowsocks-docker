# shadowsocks-docker

shadowsocks+obfs的docker封装

docker-compose文件

```yaml
version: "2"
services:
  server:
    image: kaive/shadowsocks-libev
    ports:
      - 443:443
      - 443:443/udp
    volumes:
      - ./server.json:/server.json
    command: [ "ss-server", "-c","/server.json","-u"]
    build: .
    restart: always
    container_name: ss-server

  local:
    image: kaive/shadowsocks-libev
    ports:
      - 1080:1080
      - 1080:1080/udp
    volumes:
      - ./client.json:/client.json
    command: [ "ss-local", "-c","/client.json","-u"]
    build: .
    container_name: ss-local

  redir:
    image: kaive/shadowsocks-libev
    ports:
      - 1081:1080
      - 1081:1080/udp
    volumes:
      - ./client.json:/client.json
    command: [ "ss-redir", "-c","/client.json"]
    build: .
    container_name: ss-redir

  tunnel:
    image: kaive/shadowsocks-libev
    ports:
      - 5300:1080
      - 5300:1080/udp
    volumes:
      - ./client.json:/client.json
    command: [ "ss-tunnel", "-c","/client.json","-L","8.8.8.8:53","-u"]
    build: .
    container_name: ss-tunnel
```

server.json

```json
{
  "server": "0.0.0.0",
  "server_port": 443,
  "password": "YourPassWord",
  "timeout": 300,
  "method": "chacha20",
  "fast_open": true,
  "workers": 1,
  "prefer_ipv6": false,
  "plugin": "obfs-server",
  "plugin_opts": "obfs=tls"
}
```

client.json
```json
{
  "server": "YourHost",
  "server_port": 443,
  "local_address": "0.0.0.0",
  "local_port": 1080,
  "password": "YourPassWord",
  "timeout": 300,
  "method": "chacha20",
  "fast_open": true,
  "workers": 1,
  "prefer_ipv6": false,
  "plugin": "obfs-local",
  "plugin_opts": "obfs=tls;obfs-host=download.windowsupdate.com"
}
```

使用方法-Compose

```docker-compose up server```

```docker-compose up local```

```docker-compose up redir```

```docker-compose up tunnel```