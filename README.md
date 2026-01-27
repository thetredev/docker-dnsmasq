# docker-dnsmasq

dnsmasq in a docker container, configurable via a [simple web UI](https://github.com/jpillora/webproc)

## Usage

### 1. dnsmasq.conf

Create a `dnsmasq.conf` file somewhere on the Docker host, e.g. `/opt/dnsmasq.conf`. See http://oss.segetech.com/intra/srv/dnsmasq.conf for some more details.
```ini
#dnsmasq config, for a complete example, see:
#  http://oss.segetech.com/intra/srv/dnsmasq.conf
#log all dns queries
log-queries
#dont use hosts nameservers
no-resolv
#use cloudflare as default nameservers, prefer 1^4
server=1.0.0.1
server=1.1.1.1
strict-order
#serve all .company queries using a specific nameserver
server=/company/10.0.0.1
#explicitly define host-ip mappings
address=/myhost.company/10.0.0.2
```

### 2. Run the container
This repository provides a [compose.yaml](compose.yaml) file which you could use as-is. Be sure to set the release version, though! See more on that further down this document.

Alternatively, you could execute `docker run` directly like so:
```
docker run \
  --name dnsmasq \
  -d \
  -p 53:53/udp \
  -p 5380:8080 \
  -v /opt/dnsmasq.conf:/etc/dnsmasq.conf \
  --log-opt "max-size=100m" \
  -e "HTTP_USER=foo" \
  -e "HTTP_PASS=bar" \
  --restart unless-stopped \
  ghcr.io/thetredev/docker-dnsmasq:<release>
```

where `<release>` corresponds to any of the [Git tags](https://github.com/thetredev/docker-dnsmasq/tags) published in this project. If you want to run the development version, use
```
ghcr.io/thetredev/docker-dnsmasq:develop
```

### 3. Test DNS lookups
```
host myhost.company 127.0.0.1
```

should yield:
```
Using domain server:
Name: 127.0.0.1
Address: 127.0.0.1#53
Aliases:

myhost.company has address 10.0.0.2
```

### 4. Test webproc

Visit `http://127.0.0.1:5380`, authenticate with `foo/bar` and you should see something like this:

<img width="833" alt="screen shot 2017-10-15 at 1 41 21 am" src="https://user-images.githubusercontent.com/633843/31580966-baacba62-b1a9-11e7-8439-ca1ddfe828dd.png">

## MIT License

Copyright &copy; 2018 Jaime Pillora &lt;dev@jpillora.com&gt;<br/>
Copyright &copy; 2026 Timo Reichl &lt;thetredev@gmail.com&gt;

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

[dockerhub]: https://hub.docker.com/r/jpillora/dnsmasq/
