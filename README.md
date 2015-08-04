# StreamMachine Multi-Machine Vagrant Environment

This is a simple and automated multi-machine Vagrant testing environment for [StreamMachine](https://github.com/StreamMachine/StreamMachine).

## Environment

The Vagrant box used is `chef/ubuntu-14.04`. I'm using the Chef box because it works with VMware, but we're not using Chef cookbooks here.

## Network Topology

|    Host    |       IP       |
| ---------- | -------------- |
| standalone | 192.168.200.10 |
| master     | 192.168.200.11 |
| slave      | 192.168.200.12 |
| redis      | 192.168.200.13 |

## Standalone Setups

### Standalone StreamMachine

Start the standalone VM:

    $ vagrant up standalone
    $ vagrant ssh standalone
    cd /srv/StreamMachine
    sudo ./streammachine-cmd --config /vagrant/config/standalone.json

### Standalone StreamMachine with Redis

Start the redis VM:

    $ vagrant up redis

Start the standalone VM:

    $ vagrant up standalone
    $ vagrant ssh standalone
    cd /srv/StreamMachine
    sudo ./streammachine-cmd --config /vagrant/config/standalone-redis.json

Create the stream named `my_new_stream`:

    $ curl -F 'key=my_new_stream' http://192.168.200.10:8001/api/streams

Feed the base configuration found under `config/my_new_stream.json`:

    $ curl -H "Content-Type: application/json" -H 'Accept: application/json' -X PUT --data @config/my_new_stream.json http://192.168.200.10:8001/api/streams/my_new_stream/config

Redis is now the backend storage for StreamMachine configuration.

### Standalone Usage

You can now stream (ie. icecast2) to `192.168.200.10:8002` using the test credentials and listen to the stream on `http://192.168.200.10:8001/test` (or use an HLS-enabled device like Safari and use http://192.168.200.10:8001/test.m3u8)

## Master StreamMachine

Start the redis VM:

    $ vagrant up redis

Start the master VM:

    $ vagrant up master
    $ vagrant ssh master
    cd /srv/StreamMachine
    sudo ./streammachine-cmd --config /vagrant/config/master.json

You can now stream (ie. icecast2) to `192.168.200.11:8002` using the test credentials. You'll need a slave service to listen to the stream.

## Slave StreamMachine

Start the slave VM:

    $ vagrant up slave
    cd /srv/StreamMachine
    sudo ./streammachine-cmd --config /vagrant/config/slave.json
