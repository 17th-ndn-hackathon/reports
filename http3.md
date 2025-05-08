# HTTP/3 WebTransport pilot deployment

## Server Preparation

<br />

```Shell
# assign IP address
sudo ip a a 2a0a:6040:a317::69d1:5b87 dev lo
# (insert an AAAA record for ndnhack17.yoursunny.dev to the above IP address)

# in firewall, allow port 443 for TCP (WebSocket) and UDP (WebTransport)
sudo ufw allow to 2a0a:6040:a317::69d1:5b87 port 443 proto tcp
sudo ufw allow to 2a0a:6040:a317::69d1:5b87 port 443 proto udp

# obtain certificate
acme.sh --issue --keylength ec-256 --dns --yes-I-know-dns-manual-mode-enough-go-ahead-please -d ndnhack17.yoursunny.dev
# (follow instruction to insert TXT record manually)
acme.sh --issue --keylength ec-256 --dns --yes-I-know-dns-manual-mode-enough-go-ahead-please -d ndnhack17.yoursunny.dev --renew
```

<br />

## Compile and Run

```YAML
# ~/yanfd.yml
# only modified .faces.websocket and .faces.webtransport sections

core:
  # Logging level
  log_level: TRACE
  # Output log to file
  log_file: ""

faces:
  # Size of queues in the face system
  queue_size: 1024
  # Enables or disables congestion marking
  congestion_marking: true
  # If true, face threads will be locked to processor cores
  lock_threads_to_cores: false

  udp:
    # Whether to enable unicast UDP listener
    enabled_unicast: true
    # Whether to enable multicast UDP listener
    enabled_multicast: true
    # Port used for unicast UDP faces
    port_unicast: 6363
    # Port used for multicast UDP faces
    port_multicast: 56363
    # IPv4 address used for multicast UDP faces
    multicast_address_ipv4: 224.0.23.170
    # IPv6 address used for multicast UDP faces
    multicast_address_ipv6: ff02::114
    # Lifetime of on-demand faces (in seconds)
    lifetime: 600
    # Default MTU for UDP faces
    default_mtu: 1420

  tcp:
    # Whether to enable TCP listener
    enabled: true
    # Port used for unicast TCP faces
    port_unicast: 6363
    # Lifetime of on-demand faces (in seconds)
    lifetime: 600
    # Reconnect interval for permanent faces (in seconds)
    reconnect_interval: 10

  unix:
    # Whether to enable Unix stream transports
    enabled: true
    # Location of the socket file
    socket_path: /run/nfd/nfd.sock

  websocket:
    # Whether to enable WebSocket listener
    enabled: true
    # Bind address for WebSocket listener
    bind: ""
    # Port for WebSocket listener
    port: 443
    # Whether to enable TLS
    tls_enabled: true
    # TLS certificate path (relative to the config file)
    tls_cert: "/home/sunny/.acme.sh/ndnhack17.yoursunny.dev_ecc/fullchain.cer"
    # TLS private key (relative to the config file)
    tls_key: "/home/sunny/.acme.sh/ndnhack17.yoursunny.dev_ecc/ndnhack17.yoursunny.dev.key"

  http3:
    enabled: true
    bind: ""
    port: 443
    tls_cert: "/home/sunny/.acme.sh/ndnhack17.yoursunny.dev_ecc/fullchain.cer"
    tls_key: "/home/sunny/.acme.sh/ndnhack17.yoursunny.dev_ecc/ndnhack17.yoursunny.dev.key"

fw:
  # Number of forwarding threads
  threads: 8
  # Size of queues in the forwarding system
  queue_size: 1024
  # If true, face threads will be locked to processor cores
  lock_threads_to_cores: false

mgmt:
  # Controls whether management over /localhop is enabled or disabled
  allow_localhop: false

tables:

  content_store:
    # Capacity of each forwarding thread's content store (in number of Data packets). Note that the
    # total capacity of all content stores in the forwarder will be the number of threads
    # multiplied by this value. This is the startup configuration value and can be changed at
    # runtime via management.
    capacity: 1024
    # Whether contents will be admitted to the Content Store.
    admit: true
    # Whether contents will be served from the Content Store.
    serve: true
    # Cache replacement policy to use in each thread's content store.
    replacement_policy: lru

  dead_nonce_list:
    # Lifetime of entries in the Dead Nonce List (milliseconds)
    lifetime: 6000

  network_region:
    # List of prefixes that the forwarder is in the producer region for
    regions: []

  rib:
    # Enables or disables readvertising to the routing daemon
    readvertise_nlsr: true

  fib:
    # Selects the algorithm used to implement the FIB
    # Allowed options: nametree, hashtable
    algorithm: nametree

    hashtable:
      # Specifies the virtual node depth. Must be a positive number.
      m: 5

```

```Shell
# compile and run forwarder
make && sudo ./ndnd fw run ~/yanfd.yml

# create route to ndn6 network
./ndnd fw route-add face=udp6://[2602:ffc8:1:a352:1ccc::9d63]:6363 prefix=/yoursunny
```

## Client Side

1. Open <https://pushups.ndn.today>
2. Expand **Preferences** and enter `https://ndnhack17.yoursunny.dev/ndn` as preferred router.
3. Refresh the page and click on a title.
4. Verify that **Router** shows the configured router, instead of fallback to another router.


