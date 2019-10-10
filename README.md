# docker-zeek

> [Zeek Network Security Monitor with Kafka Integration](https://github.com/zeek/zeek) Dockerfile
> Based on work by [blacktop](https://github.com/blacktop/docker-zeek)
---

**Table of Contents**

- [Dependencies](#dependencies)
- [Installation](#installation)
- [Getting Started](#getting-started)
- [License](#license)

## Dependencies

- [alpine:3.10](https://hub.docker.com/_/alpine/)

## Installation

1. Install [Docker](https://docs.docker.com).
1. Clone this repository
1. `docker-compose -f docker-compose.yml up --build`

## Getting Started

- Adjust your Zeek interface in `docker-compose.yml`

Zeek alerts will be logged into Kafka as well as files in the `pcap` directory

```bash
$ ls -l pcap

-rw-r--r--  1 blacktop  staff   635B Jul 30 12:11 conn.log
-rw-r--r--  1 blacktop  staff   754B Jul 30 12:11 files.log
-rw-r--r--  1 blacktop  staff   384B Jul 30 12:11 known_certs.log
-rw-r--r--  1 blacktop  staff   239B Jul 30 12:11 known_hosts.log
-rw-r--r--  1 blacktop  staff   271B Jul 30 12:11 known_services.log
-rw-r--r--  1 blacktop  staff    17K Jul 30 12:11 loaded_scripts.log
-rw-r--r--  1 blacktop  staff   1.9K Jul 30 12:11 notice.log <====== NOTICE
-rw-r--r--  1 blacktop  staff   253B Jul 30 12:11 packet_filter.log
-rw-r--r--  1 blacktop  staff   1.2K Jul 30 12:11 ssl.log
-rw-r--r--  1 blacktop  staff   901B Jul 30 12:11 x509.log
```

```bash
$ cat notice.log | awk '{ print $11 }' | tail -n4

Heartbleed::SSL_Heartbeat_Attack
Heartbleed::SSL_Heartbeat_Odd_Length
Heartbleed::SSL_Heartbeat_Attack_Success
```

To test Kafka alerts set up a simple consumer

```python
import json
from kafka import KafkaConsumer

consumer = KafkaConsumer('zeek', bootstrap_servers=['172.17.0.1:9092'],
     value_deserializer=lambda x: json.loads(x.decode('utf-8')))

# Now lets process our Kafka Messages
for message in consumer:
    print(message.value)
```

## License

MIT Copyright (c) 2018 **blacktop**
