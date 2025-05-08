# ndnhack17 - consolidated NDNd project

Members: @yoursunny, @a-thieme

***

## NDN-FCH client

Status:

* Copied / adapted from NDNgo library.

* The FCH client uses a JSON-tagged struct to decode the response from FCH 2021 server, in which a *prefix* field is an NDN name.

  * NDNgo's `ndn.Name` type implements `encoding.TextUnmarshaler` interface, so that it can be used as a field in a JSON struct.

  * In contrast, ndnd's Name type does not implement this interface.

  * For now, this field is changed to `string` type, which works but is less strict.

* The implementation has been validated against NDN-FCH 2021 (`https://fch.ndn.today`) and NDN-FCH 2016 (`http://ndn-fch.named-data.net`).

* Works as a library for native go programs

Future:

* Test this feature in browser after compiling as WebAssembly module. (integration with ownly)

Link: <https://github.com/named-data/ndnd/pull/139>&#x20;

<br />

***

## HTTP/3 WebTransport listener/transport

Status: simple version implemented.

* FaceUri is defined to be `quic://host:port` for both client and server.

* Pilot deployment at `https://ndnhack17.yoursunny.dev/ndn` with an TLS certificate obtained with **acme.sh** client.

* Tested with NDNts-video by entering preferred router

Remaining issues:

* MTU is fixed at 1000 octets, less than expected 1200 octets.

  * While **quic-go** supports MTU discovery, it does not return the current maximum datagram size, but only reports the datagram we are trying to send is too large.

  * On the other hand, YaNFD expects the transport to report its own MTU. Otherwise, the send failure caused by exceeding datagram size limit would result in face closure.

  * We need to find a way to extract effective MTU for QUIC datagrams out of **quic-go**.

Future:

* Both WebSocket and WebTransport configuration has `TLSCert` and `TLSKey` options.\
  These can be deduplicated: make one of them optional, adopting the other value as default.

* `Bind` and `Port` options are too limiting because you can only specify one host:port combination.

  * Current pilot deployment uses only a singular IPv6 address, so that I can write the IP address in the `Bind` option.
  * I can also keep the `Bind` option empty, to make YaNFD listen on all IP addresses.
  * It isn't possible to make YaNFD listen on several but not all IP addresses.
  * The use case is a server with two IPv4 and two IPv6, I may want Caddy on `192.0.2.1 3fff:57:1895:1` and YaNFD on `192.0.2.2 3fff:57:1895::1`.
  * The solution is configuring with `[]netip.AddrPort` instead of separate `Bind Port` fields, which also requires changing the listener creation procedure.

* It may be beneficial to implement automatic TLS certificate acquisition and renewal.

  * Typically TLS certificate lasts 90 days now, but the maximum lifetime will reduce to 47 days by 2029.

  * While **acme.sh** can obtain and renew certificate, YaNFD must restart to apply new certificates.

  * We could either adopt CertMagic library to automatically obtain and renew TLS certificates, or at least allow triggering certificate reload with a Unix signal.

Link: <https://github.com/named-data/ndnd/pull/138>

***

## Ethernet transport

Status: investigated but did not write code.

**gopacket/pcap** library:

* Upon inspection, the old code relies on `gopacket/pcap`, which calls into libpcap and thus requires Cgo.

* YaNFD is a pure Go application and cannot use Cgo.

* Thus, it cannot be adopted directly.

**gopacket/afpacket** library:

* The Ethernet transport can be implemented with `gopacket/afpacket` library.

* This will only work on Linux.

* However, `gopacket/afpacket` package has race conditions when trying to close a face (this was noticed during NDN-DPDK development).

* Due to this race condition, it is only safe to implement multicast Ethernet transport, which does not need to be closed.

* It is risky to implement unicast Ethernet transport, which must be closable.

**mdlayher/packet** library:

* I recalled that NDNgo has switched to `mdlayher/packet` library, which is a thin wrapper around AF\_PACKET socket (thus, Linux only).
* "The connect(2) operation is not supported on packet sockets".
* A single `packet.Conn` serves both the multicast Ethernet transport and all unicast Ethernet transports on the same netif.
* This is substantially different from other YaNFD transports, where each transport instance has more-or-less an independent `net.Conn` instance to be used within.

***

## File Server

We did not have time for this part.


