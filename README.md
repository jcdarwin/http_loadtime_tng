HTTP loadtime TNG
=================

A Webpage loadtime plugin for Munin (forked from [https://github.com/metaltoad/http_loadtime_tng](https://github.com/metaltoad/http_loadtime_tng))

Please see https://www.metaltoad.com/blog/keeping-track-page-load-times-munin for more information.

## Preparation

On our Ubuntu 12.04 installation:

    apt-get install wget
    ruby -v
    apt-get install rubygems
    apt-get install ruby-daemons
    gem install daemons
    apt-get install imagemagick libmagickwand-dev
    gem install rmagick
    gem install fileutils

## Configuration

Configure the Munin `http_loadtime` plugin by adding a file `/etc/munin/plugin-conf.d/http_loadtime` with the following content:

    [http_loadtime]
        timeout 30
        env.names url1
        env.timeout 30
        env.error_value 60
        env.max 120

    #   mebooks.co.nz
        env.url_url1 mebooks.co.nz
        env.label_url1 mebooks.co.nz
        env.proto_url1 https
        env.port_url1 443
    #    env.path_url1 path2/page2
        env.timeout_url1 30
        env.warning_url1 15
        env.critical_url1 20
        env.wget_opts_url1 --no-cache --tries=1 --no-check-certificate

## Installation

Clone and move the `http_loadtime` plugin to the plugins directory:

    cd /tmp
    git clone git@github.com:jcdarwin/http_loadtime_tng.git
    cp http_loadtime_tng/http_loadtime.conf /etc/munin/plugin-conf.d/http_loadtime
    cp http_loadtime_tng/http_loadtime /usr/share/munin/plugins/
    cp http_loadtime_tng/http_loadtime_launcher.rb /usr/share/munin/plugins/
    cp http_loadtime_tng/http_loadtime_daemon.rb /usr/share/munin/plugins/
    chmod a+x /usr/share/munin/plugins/http*

Enable the `http_loadtime` plugin:

    cd /etc/munin/plugins/
    ln -s /usr/share/munin/plugins/http_loadtime .

Restart Munin node and wait for the munin-node cron job (typically 5 mins):

    /etc/init.d/munin-node restart

## Testing

munin-run http_loadtime config
munin-run http_loadtime

The actual wget command will look something like the following:

    wget --no-cache --no-check-certificate --save-headers --no-directories --output-document /var/run/munin/http_loadtime//tmoutput_journalsurf.co.nz --header="Host:journalsurf.co.nz" http://journalsurf.co.nz > /dev/null 2>&1
