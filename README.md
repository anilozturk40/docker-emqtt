# EMQ Docker
EMQ (Erlang MQTT Broker) is a distributed, massively scalable, highly extensible MQTT message broker written in Erlang/OTP.

### Get emqttd

You can build this docker image by yourself.
```bash
git clone -b master https://github.com/anilozturk40/docker-emqtt.git
cd docker-emqtt
docker build -t emq:latest .
```
### Run emqttd

Execute some command under this docker image

```docker run --rm -ti -v `pwd`:$(somewhere) emq/$(image) $(somecommand)```

For example

```docker run --rm -ti --name emq -p 18083:18083 -p 1883:1883 emq:latest```

The emqtt erlang broker runs as linux user emqtt in the docker container.

### Configuration

Use the environment variable to configure the EMQ docker container

The environment variables which with ``EMQ_`` prefix are mapped to configuration file, ``.`` get replaced by ``__``.

Example:

```bash
EMQ_LISTENER__SSL__EXTERNAL__ACCEPTORS <--> listener.ssl.external.acceptors
EMQ_MQTT__MAX_PACKET_SIZE              <--> mqtt.max_packet_size
```

Also the environment variables which with ``PLATFORM_`` prefix are mapped to template string in configuration file.

```bash
PLATFORM_ETC_DIR                   <--> {{ platform_etc_dir }}
```

Non mapped environment variables:

```bash
EMQ_NAME
EMQ_HOST
```

These environment variables will ignore for configuration file.

#### EMQ Configuration

> NOTE: All EMQ Configuration in [etc/emq.conf](https://github.com/emqtt/emqttd/blob/master/etc/emq.conf) could config by environment. The following list is just an example, not a complete configuration.

| Options                    | Default            | Mapped                    | Description                           |
| ---------------------------| ------------------ | ------------------------- | ------------------------------------- |
| EMQ_NAME                   | container name     | none                      | emq node short name                   |
| EMQ_HOST                   | container IP       | none                      | emq node host, IP or FQDN             |
| EMQ_WAIT_TIME              | 5                  | none                      | wait time in sec before timeout       |
| EMQ_JOIN_CLUSTER           | none               | none                      | Initial cluster to join               |
| EMQ_ADMIN_PASSWORD         | public             | none                      | emq admin password                    |
| PLATFORM_ETC_DIR           | /opt/emqtt/etc     | {{ platform_etc_dir }}    | The etc directory                     |
| PLATFORM_LOG_DIR           | /opt/emqtt/log     | {{ platform_log_dir }}    | The log directory                     |
| EMQ_NODE__NAME             | EMQ_NAME@EMQ_HOST  | node.name                 | Erlang node name, name@ipaddress/host |
| EMQ_NODE__COOKIE           | emq_dist_cookie    | node.cookie               | cookie for cluster                    |
| EMQ_LOG__CONSOLE           | console            | log.console               | log console output method             |
| EMQ_MQTT__ALLOW_ANONYMOUS  | true               | mqtt.allow_anonymous      | allow mqtt anonymous login            |
| EMQ_LISTENER__TCP__EXTERNAL| 1883               | listener.tcp.external     | MQTT TCP port                         |
| EMQ_LISTENER__SSL__EXTERNAL| 8883               | listener.ssl.external     | MQTT TCP TLS/SSL port                 |
| EMQ_LISTENER__WS__EXTERNAL | 8083               | listener.ws.external      | HTTP and WebSocket port               |
| EMQ_LISTENER__WSS__EXTERNAL| 8084               | listener.wss.external     | HTTPS and WSS port                    |
| EMQ_LISTENER__API__MGMT    | 8080               | listener.api.mgmt         | mgmt API  port                        |
| EMQ_MQTT__MAX_PACKET_SIZE  | 64KB               | mqtt.max_packet_size      | Max Packet Size Allowed               |

The list is incomplete and may changed with [etc/emq.conf](https://github.com/emqtt/emqttd/blob/master/etc/emq.conf) and plugin configuration files. But the mapping rule is similar.

If set ``EMQ_NAME`` and ``EMQ_HOST``, and unset ``EMQ_NODE__NAME``, ``EMQ_NODE__NAME=$EMQ_NAME@$EMQ_HOST``.

For example, set mqtt tcp port to 1883

``docker run --rm -ti --name emq -e EMQ_LISTENER__TCP__EXTERNAL=1883 -p 18083:18083 -p 1883:1883 emq:latest``
