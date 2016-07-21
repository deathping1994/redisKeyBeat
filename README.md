[![Build Status](https://travis-ci.org/chrsblck/redisbeat.svg?branch=master)](https://travis-ci.org/chrsblck/redisbeat)
[![codecov.io](https://codecov.io/github/chrsblck/redisbeat/coverage.svg?branch=master)](https://codecov.io/github/chrsblck/redisbeat?branch=master)

# RedisKeybeat

RedisKeybeat is an extension of RedisBeat - the Beat used for Redis monitoring. RedisKeyBeat adds the ability to query keys from redis and publish them as events periodically.It has retained the features offered by RedisBeat, so it also does everything the original RedisBeat did.

## Use Case 
- It is useful if you use redis as central data store for vital stats of other services
- If you have cron jobs running that collect data from logs and store them into redis
- If you want to visualize data stored in redis (Like customer activity and behavior)



## Elasticsearch template

To apply Redisbeat template for Redis status:

```
curl -XPUT 'http://localhost:9200/_template/redisbeat' -d@etc/redisbeat.template.json
```


# Build, Test, Run

```
# Build
export GO15VENDOREXPERIMENT=1
GOPATH=<your go path> make

# Test
GOPATH=<your go path> make unit-tests

# Run Test Suite
GOPATH=<your go path> make testsuite

# Run
./redisbeat -c redisbeat.yml
```


## Exported fields

RedisKeyBeat supports `LIST`, `HASH` and `STRING` keys from redis, it takes a pattern and exports all the keys matching the pattern.

### Sample event from `LIST` key (All the string keys are grouped together and exported in single event)

```
{
  "_index": "redisbeat-2016.07.21",
  "_type": "list_keys",
  "_id": "AVYNB5AP7Tv348UCguHo",
  "_score": null,
  "_source": {
    "@timestamp": "2016-07-21T10:36:53.676Z",
    "beat": {
      "hostname": "gaurav-laptop",
      "name": "gaurav-laptop"
    },
    "type": "list_keys",
    "useop": [
      "4",
      "5",
      "6",
      "7"
    ]
  },
  "fields": {
    "@timestamp": [
      1469097413676
    ]
  },
  "sort": [
    1469097413676
  ]
}
```
### Sample event from `HASH` key

```
{
  "_index": "redisbeat-2016.07.21",
  "_type": "hash_key",
  "_id": "AVYNB5AP7Tv348UCguHn",
  "_score": null,
  "_source": {
    "@timestamp": "2016-07-21T10:36:53.676Z",
    "beat": {
      "hostname": "gaurav-laptop",
      "name": "gaurav-laptop"
    },
    "birthyear": "1977",
    "type": "hash_key",
    "username": "antirez",
    "verified": "1"
  },
  "fields": {
    "@timestamp": [
      1469097413676
    ]
  },
  "sort": [
    1469097413676
  ]
}
```
### Sample events from `STRING` keys (All the string keys are grouped together and exported in single event)

```
{
  "_index": "redisbeat-2016.07.21",
  "_type": "string_keys",
  "_id": "AVYNB5AP7Tv348UCguHm",
  "_score": null,
  "_source": {
    "@timestamp": "2016-07-21T10:36:53.675Z",
    "beat": {
      "hostname": "gaurav-laptop",
      "name": "gaurav-laptop"
    },
    "gra": "dss",
    "type": "string_keys",
    "user": "gauravashutosh",
    "userhulu": "djsd",
    "userhuu": "djsd",
    "userhuudsad": "djsdasdas",
    "username": "jugnu"
  },
  "fields": {
    "@timestamp": [
      1469097413675
    ]
  },
  "sort": [
    1469097413675
  ]
}
```

### Tinkering with the configuration

Settings can be altered by making changes in the redisbeat.yml, there's a sample configuration provided in the sample-redisbeat.yml

#### New Fields in redisbeat.yml
- `cache_expiry_time: <seconds>`
+ RedisKeyBeat stores the keys matching a pattern for 5 minutes by default you can alter that time by changing the `cache_expiry_time: <seconds>`
- `key_pattern_limit: <integer>`
+ By default we export a limited number of keys (Default 10, use -1 for all) of each `SUPPORTED_TYPE` that match the pattern, the setting can be changed here
- `redis_beat_cache_key: <string>`
+ This is the key RedisKeyBeat uses to cache key patterns, change it if it conflicts with your existing keys
- `keypattern: <list>`
+ Here you can specify the list of patterns you want to match. For example -
```
keypattern:
  - <string>
  - <string>
```

### Features Retained from Parent Project
Redisbeat exports each `INFO` section.

- `type: server` General information about the Redis server
- `type: clients` Client connections section  
- `type: memory` Memory consumption related information 
- `type: persistence` RDB and AOF related information
- `type: stats` General statistics
- `type: replication` Master/slave replication information
- `type: cpu` CPU consumption statistics
- `type: commandstats` Redis command statistics
- `type: cluster` Redis Cluster section
- `type: keyspace` Database related statistics

**Server stats:**
```
{
	"_index": "redisbeat-2016.01.27",
	"_type": "server",
	"_id": "AVKAgQBCRp3uTbpE00b9",
	"_version": 1,
	"_score": 1,
	"_source": {
		"@timestamp": "2016-01-27T00:34:42.952Z",
		"beat": {
			"hostname": "localhost",
			"name": "localhost"
		},
		"count": 1,
		"stats": {
			"arch_bits": "64",
			"config_file": "/etc/redis/redis-config.conf",
			"gcc_version": "4.8.2",
			"hz": "10",
			"lru_clock": "11014306",
			"multiplexing_api": "epoll",
			"os": "Linux 3.16.0-37-generic x86_64",
			"process_id": "26866",
			"redis_build_id": "7b6a15f737658530",
			"redis_git_dirty": "0",
			"redis_git_sha1": "00000000",
			"redis_mode": "standalone",
			"redis_version": "3.0.0",
			"run_id": "973eb4c0772fe7846015e295c1ddaa0cd4d92b96",
			"tcp_port": "6379",
			"uptime_in_days": "6",
			"uptime_in_seconds": "519706"
		},
		"type": "server"
	}
}
```

**Clients stats:**
```
{
	"_index": "redisbeat-2016.01.27",
	"_type": "clients",
	"_id": "AVKAgXVxRp3uTbpE00cc",
	"_version": 1,
	"_score": 1,
	"_source": {
		"@timestamp": "2016-01-27T00:35:12.963Z",
		"beat": {
			"hostname": "localhost",
			"name": "localhost"
		},
		"count": 1,
		"stats": {
			"blocked_clients": "0",
			"client_biggest_input_buf": "0",
			"client_longest_output_list": "0",
			"connected_clients": "3"
		},
		"type": "clients"
	}
}
```
**Memory stats:**
```
{
	"_index": "redisbeat-2016.01.27",
	"_type": "memory",
	"_id": "AVKAgZyARp3uTbpE00cn",
	"_version": 1,
	"_score": 1,
	"_source": {
		"@timestamp": "2016-01-27T00:35:22.971Z",
		"beat": {
			"hostname": "localhost",
			"name": "localhost"
		},
		"count": 1,
		"stats": {
			"mem_allocator": "jemalloc-3.6.0",
			"mem_fragmentation_ratio": "0.98",
			"used_memory": "2839218984",
			"used_memory_human": "2.64G",
			"used_memory_lua": "35840",
			"used_memory_peak": "2839451672",
			"used_memory_peak_human": "2.64G",
			"used_memory_rss": "2791378944"
		},
		"type": "memory"
	}
}
```
**Persistence stats:**
```
{
	"_index": "redisbeat-2016.01.27",
	"_type": "persistence",
	"_id": "AVKAgQBCRp3uTbpE00cA",
	"_version": 1,
	"_score": 1,
	"_source": {
		"@timestamp": "2016-01-27T00:34:42.979Z",
		"beat": {
			"hostname": "localhost",
			"name": "localhost"
		},
		"count": 1,
		"stats": {
			"aof_base_size": "2141338459",
			"aof_buffer_length": "0",
			"aof_current_rewrite_time_sec": "-1",
			"aof_current_size": "2148807529",
			"aof_delayed_fsync": "1",
			"aof_enabled": "1",
			"aof_last_bgrewrite_status": "ok",
			"aof_last_rewrite_time_sec": "-1",
			"aof_last_write_status": "ok",
			"aof_pending_bio_fsync": "0",
			"aof_pending_rewrite": "0",
			"aof_rewrite_buffer_length": "0",
			"aof_rewrite_in_progress": "0",
			"aof_rewrite_scheduled": "0",
			"loading": "0",
			"rdb_bgsave_in_progress": "0",
			"rdb_changes_since_last_save": "0",
			"rdb_current_bgsave_time_sec": "-1",
			"rdb_last_bgsave_status": "ok",
			"rdb_last_bgsave_time_sec": "36",
			"rdb_last_save_time": "1453843449"
		},
		"type": "persistence"
	}
}
```
**Redis-stats:**
```
{
	"_index": "redisbeat-2016.01.27",
	"_type": "stats",
	"_id": "AVKAgNvXRp3uTbpE00b3",
	"_version": 1,
	"_score": 1,
	"_source": {
		"@timestamp": "2016-01-27T00:34:32.993Z",
		"beat": {
			"hostname": "localhost",
			"name": "localhost"
		},
		"count": 1,
		"stats": {
			"evicted_keys": "0",
			"expired_keys": "0",
			"instantaneous_input_kbps": "0.03",
			"instantaneous_ops_per_sec": "1",
			"instantaneous_output_kbps": "0.33",
			"keyspace_hits": "487338",
			"keyspace_misses": "10",
			"latest_fork_usec": "39656",
			"migrate_cached_sockets": "0",
			"pubsub_channels": "0",
			"pubsub_patterns": "0",
			"rejected_connections": "0",
			"sync_full": "0",
			"sync_partial_err": "0",
			"sync_partial_ok": "0",
			"total_commands_processed": "523535",
			"total_connections_received": "385",
			"total_net_input_bytes": "39050370",
			"total_net_output_bytes": "117696185"
		},
		"type": "stats"
	}
}
```
**Replication stats:**
```
{
	"_index": "redisbeat-2016.01.27",
	"_type": "replication",
	"_id": "AVKAhRIoRp3uTbpE00c0",
	"_version": 1,
	"_score": 1,
	"_source": {
		"@timestamp": "2016-01-27T00:39:09.700Z",
		"beat": {
			"hostname": "localhost",
			"name": "localhost"
		},
		"count": 1,
		"stats": {
			"connected_slaves": "0",
			"master_repl_offset": "0",
			"repl_backlog_active": "0",
			"repl_backlog_first_byte_offset": "0",
			"repl_backlog_histlen": "0",
			"repl_backlog_size": "1048576",
			"role": "master"
		},
		"type": "replication"
	}
}
```
**Cpu stats:**
```
{
	"_index": "redisbeat-2016.01.27",
	"_type": "cpu",
	"_id": "AVKAgQBCRp3uTbpE00cD",
	"_version": 1,
	"_score": 1,
	"_source": {
		"@timestamp": "2016-01-27T00:34:43.008Z",
		"beat": {
			"hostname": "localhost",
			"name": "localhost"
		},
		"count": 1,
		"stats": {
			"used_cpu_sys": "450.40",
			"used_cpu_sys_children": "35.24",
			"used_cpu_user": "185.76",
			"used_cpu_user_children": "273.30"
		},
		"type": "cpu"
	}
}
```
**Commandstats:**
```
{
	"_index": "redisbeat-2016.01.27",
	"_type": "commandstats",
	"_id": "AVKAgSdSRp3uTbpE00cO",
	"_version": 1,
	"_score": 1,
	"_source": {
		"@timestamp": "2016-01-27T00:34:53.027Z",
		"beat": {
			"hostname": "localhost",
			"name": "localhost"
		},
		"count": 1,
		"stats": {
			"cmdstat_auth": "calls=337,usec=2031,usec_per_call=6.03",
			"cmdstat_dbsize": "calls=2,usec=8,usec_per_call=4.00",
			"cmdstat_flushdb": "calls=1,usec=8155,usec_per_call=8155.00",
			"cmdstat_get": "calls=162264,usec=294211,usec_per_call=1.81",
			"cmdstat_hexists": "calls=308,usec=14478,usec_per_call=47.01",
			"cmdstat_hget": "calls=324527,usec=700351,usec_per_call=2.16",
			"cmdstat_hgetall": "calls=249,usec=145421,usec_per_call=584.02",
			"cmdstat_hset": "calls=30060,usec=143089,usec_per_call=4.76",
			"cmdstat_info": "calls=3546,usec=159072,usec_per_call=44.86",
			"cmdstat_keys": "calls=688,usec=269581,usec_per_call=391.83",
			"cmdstat_ping": "calls=2,usec=10,usec_per_call=5.00",
			"cmdstat_select": "calls=270,usec=1408,usec_per_call=5.21",
			"cmdstat_set": "calls=1304,usec=4397,usec_per_call=3.37"
		},
		"type": "commandstats"
	}
}
```
**Cluster stats:**
```
{
	"_index": "redisbeat-2016.01.27",
	"_type": "cluster",
	"_id": "AVKAgZyARp3uTbpE00ct",
	"_version": 1,
	"_score": 1,
	"_source": {
		"@timestamp": "2016-01-27T00:35:23.029Z",
		"beat": {
			"hostname": "localhost",
			"name": "localhost"
		},
		"count": 1,
		"stats": {
			"cluster_enabled": "0"
		},
		"type": "cluster"
	}
}
```
**Keyspace stats:**
```
{
	"_index": "redisbeat-2016.01.27",
	"_type": "keyspace",
	"_id": "AVKAgU5jRp3uTbpE00ca",
	"_version": 1,
	"_score": 1,
	"_source": {
		"@timestamp": "2016-01-27T00:35:03.030Z",
		"beat": {
			"hostname": "localhost",
			"name": "localhost"
		},
		"count": 1,
		"stats": {
			"db0": "keys=716456,expires=0,avg_ttl=0",
			"db1": "keys=755,expires=0,avg_ttl=0",
			"db2": "keys=755,expires=0,avg_ttl=0"
		},
		"type": "keyspace"
	}
}
```
