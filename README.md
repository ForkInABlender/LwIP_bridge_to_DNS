# lwIP Userspace DNS Bridge

This project exposes the lwIP DNS responder to normal host tools such as `dig` by running a small Python UDP bridge.

The working bridge is:

```bash
python/dns_bridge.py
```

It listens on a host UDP socket, receives DNS payloads, wraps them in an Ethernet/IPv4/UDP frame, injects that frame into the userspace lwIP board, then unwraps lwIP's Ethernet response back into a normal DNS UDP reply.


## Run the DNS Bridge

From the project root:

```bash
cd /root/.openclaw/workspace/lwip_userspace
python3.10 python/dns_bridge.py
```

By default, it listens on:

```text
127.0.0.1:5353
```

And serves:

```text
example.test A 203.0.113.9
```

Expected startup output:

```text
Ethernet-framed lwIP DNS bridge listening on 127.0.0.1:5353; example.test A 203.0.113.9
```

## Query with dig

In another shell:

```bash
dig @127.0.0.1 -p 5353 A example.test
```

Expected answer:

```text
example.test.  60  IN  A  203.0.113.9
```

## Use a Different Port

If port `5353` is already occupied, run the bridge on another port:

```bash
cd /root/.openclaw/workspace/lwip_userspace
python3.10 python/dns_bridge.py --port 5356
```

Then query that port:

```bash
dig @127.0.0.1 -p 5356 A example.test
```

## Configure the Served Name or A Record

The bridge supports command-line overrides:

```bash
python3.10 python/dns_bridge.py --name example.test --answer 203.0.113.9
```

Full example:

```bash
python3.10 python/dns_bridge.py --host 127.0.0.1 --port 5353 --name example.test --answer 203.0.113.9
```

## Troubleshooting

### `dig` says no servers could be reached

Check whether the bridge is running and listening on the same host/port you are querying.

Example:

```bash
dig @127.0.0.1 -p 5353 A example.test
```

must match a bridge started with:

```bash
python3.10 python/dns_bridge.py --host 127.0.0.1 --port 5353
```
