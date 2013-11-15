StatsD Role
============

StatsD will play an aggregation and redistribution role
in our datacenter. Combined with collectd on remote machines
and backends, it then becomes possible to fan in all the stats
data, and then fan it out to different services/tools.

It will also of course be possible to stream live stats from
any application within our datacenter should we see a need for
this.

In general, we put this on a single machine, alongside the
[logstash](../logstash) and [collectd](../collectd) roles,
thus aggregating logs and metrics through a single endpoint.

However, it is very possible to decouple these services and have
dedicated machines for each.

Available backends
--------------------

Backends are forward destination to which
we will want to send the aggregated data to.

For now we support Librato (good for metrics),
instrumental (somewhat similar, with more emphasis on
real-time), graphite (similar, but self-hosted and open-source)
and DucksBoard (good for creating project-specific dashboard,
with integration for JIRA, GitHub, BaseCamp, etc).

Configuration
--------------

### inventory/my_inventory

```ini
[statsd:children]
statsd-somedc-prod

[statsd-somedc-prod]
statsd-1  ansible_ssh_host=172.16.0.93

[ ... skipping ... ]

[somedc-prod:children]
statsd-somedc-prod
```

### Task configuration example

```yaml
- name: Run the tasks for 'statsd' servers
  hosts: statsd
  gather_facts: true
  sudo: true
  user: '{{ user }}'
  accelerate: '{{ accelerated }}'   # if set to true, ensure accelerate_port is firewalled
  accelerate_port: 9210             # Note: always protect this port from outsiders
  roles:
    - common
    - nodejs # Requirement
    - statsd
```

### Execution

```bash
gameinfra deploy production production/mygame/somedc \
    --limit=statsd \
    --extra-vars="use_librato=false" \
    --extra-vars="librato_email=account@email.com" \
    --extra-vars="librato_token=ACCOUNT-TOKEN" \
    --extra-vars="use_ducksboard=false" \
    --extra-vars="ducksboard_apikey=API-KEY" \
    --extra-vars="use_instrumental=false" \
    --extra-vars="instrumental_key=API-KEY" \
    --extra-vars="use_graphite=false" \
    --extra-vars="graphite_host=graphite-1" \
    --extra-vars="graphite_port=2003"
```

See also
---------

* [About Graphite](http://graphite.wikidot.com/start)
* [Some alternative Graphite dashboards (including Graphene)](http://dashboarddude.com/blog/2013/01/23/dashboards-for-graphite/)
* [Librato](https://metrics.librato.com)
* [Librato's backend on GitHub](https://github.com/librato/statsd-librato-backend)
* [Ducksboard](https://ducksboard.com/)
* [Ducksboard backend on GitHub](https://github.com/mcuadros/statsd-ducksboard-backend)
* [Instrumental](https://instrumentalapp.com/)
* [Instrumental backend on GitHub](https://github.com/collectiveidea/statsd-instrumental-backend)
