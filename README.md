StatsD Role
============

StatsD will play an aggregation and redistribution role
in our datacenter. Combined with collectd on remote machines
and backends, it then becomes possible to fan in all the stats
data, and then fan it out to different services/tools.

It will also of course be possible to stream live stats from
any application within our datacenter should we see a need for
this.

Available backends
--------------------

Backends are forward destination to which
we will want to send the aggregated data to.

For now we support Librato (good for metrics),
instrumental (somewhat similar, with more emphasis on
real-time) and graphite (similar, but self-hosted and open-source).

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

### Execution

When provisioning with this role, you can also add [Librato](https://www.librato.com/),
[Instrumental](https://instrumentalapp.com/) or [Graphite](http://graphite.wikidot.com/)
using "** --extra-vars= **" as described below.

**Do not add any credentials to your inventory**

```bash
    --extra-vars="use_librato=false" \
    --extra-vars="librato_email=account@email.com" \
    --extra-vars="librato_token=ACCOUNT-TOKEN" \
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
* [Instrumental](https://instrumentalapp.com/)
* [Instrumental backend on GitHub](https://github.com/collectiveidea/statsd-instrumental-backend)
