# Scapy NDN

<br />

* Prototype available before hackathon to generate/decode NDN packets
* Added during the hackathon:

  * Clean up/formatting + README about the project
  * sn-nfdc tool

    * Use meta class wherever possible for packet definitions.
    * Complete Route add/remove/list options (initially, only part of add functionality was there)

      * NFD returns Success when registering route to UDP broadcast face who's FaceID has changed after my laptop wakes from sleep

        * It does not actually register the route successfully
      * My NFD version is from December 2024
    * Add Strategy list/set/unset&#x20;
    * Add ContentStore info command
* Lessons

  * Discovered that some parts like NameComponent decoding within Name can be fragile

<br />

```Python
$ sn-nfdc route --add --prefix /test/ndn3 --nexthop 311 --cost 100 
###[ Interest ]###
  type      = 5
  length    = 260
  \value     \
   |###[ Name ]###
   |  type      = 7
   |  length    = 96
   |  \value     \
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 9
   |   |  value     = b'localhost'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 3
   |   |  value     = b'nfd'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 3
   |   |  value     = b'rib'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 8
   |   |  value     = b'register'
   |   |###[ NameComponent (PktList) ]###
   |   |  type      = 8
   |   |  length    = 29
   |   |  \value     \
   |   |   |###[ ControlParameters ]###
   |   |   |  type      = 104
   |   |   |  length    = 27
   |   |   |  \value     \
   |   |   |   |###[ Name ]###
   |   |   |   |  type      = 7
   |   |   |   |  length    = 12
   |   |   |   |  \value     \
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'test'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'ndn3'
   |   |   |   |###[ FaceId ]###
   |   |   |   |  type      = 105
   |   |   |   |  length    = 2
   |   |   |   |  value     = 311 (0x137)
   |   |   |   |###[ Origin ]###
   |   |   |   |  type      = 111
   |   |   |   |  length    = 1
   |   |   |   |  value     = 255 (0xFF)
   |   |   |   |###[ Cost ]###
   |   |   |   |  type      = 106
   |   |   |   |  length    = 1
   |   |   |   |  value     = 100 (0x64)
   |   |   |   |###[ Flags ]###
   |   |   |   |  type      = 108
   |   |   |   |  length    = 1
   |   |   |   |  value     = 1 (0x01)
   |   |###[ ParametersSha256DigestComponent ]###
   |   |  type      = 2
   |   |  length    = 32
   |   |  value     = b'\t/@\x96\x7f\x04u1~J\x7f"g-\xceKE\xbbg\xf4\x03\x11\xcd\xae\x86;M\xa3\xe7\x94\xbd\xbe'
   |###[ Nonce ]###
   |  type      = 10
   |  length    = 4
   |  value     = 0x7fba19e
   |###[ ApplicationParameters ]###
   |  type      = 36
   |  length    = 0
   |  value     = b''
   |###[ InterestSignatureInfo ]###
   |  type      = 44
   |  length    = 79
   |  \value     \
   |   |###[ SignatureType ]###
   |   |  type      = 27
   |   |  length    = 1
   |   |  value     = 3 [SignatureSha256WithEcdsa]
   |   |###[ KeyLocator ]###
   |   |  type      = 28
   |   |  length    = 54
   |   |  \value     \
   |   |   |###[ Name ]###
   |   |   |  type      = 7
   |   |   |  length    = 52
   |   |   |  \value     \
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 9
   |   |   |   |  value     = b'localhost'
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 8
   |   |   |   |  value     = b'operator'
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 3
   |   |   |   |  value     = b'KEY'
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 8
   |   |   |   |  value     = b'\xe7B\x1bx"\x91\xed\xe0'
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 4
   |   |   |   |  value     = b'self'
   |   |   |   |###[ Version Name Component ]###
   |   |   |   |  type      = 54
   |   |   |   |  length    = 8
   |   |   |   |  value     = 1699495954653 (0x18BB1DA48DD)
   |   |###[ SignatureNonce ]###
   |   |  type      = 38
   |   |  length    = 8
   |   |  value     = b'v\xdf\xb8\xaa\xee@\x06C'
   |   |###[ SignatureTime ]###
   |   |  type      = 40
   |   |  length    = 8
   |   |  value     = 1745180870076 [2025-04-20 13:27:50.076000]
   |###[ ECDSASignatureValue ]###
   |  type      = 46
   |  length    = 71
   |  \value     \
   |   |###[ ECDSASignature ]###
   |   |  r         = 0xc53569a482...249d283d48 <ASN1_INTEGER[8920000318...4008401224]>
   |   |  s         = 0xbba6f1be2b...1f6982c9cb <ASN1_INTEGER[5304841734...0290919883]>

###[ Data ]###
  type      = 6
  length    = 188
  \value     \
   |###[ Name ]###
   |  type      = 7
   |  length    = 96
   |  \value     \
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 9
   |   |  value     = b'localhost'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 3
   |   |  value     = b'nfd'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 3
   |   |  value     = b'rib'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 8
   |   |  value     = b'register'
   |   |###[ NameComponent (PktList) ]###
   |   |  type      = 8
   |   |  length    = 29
   |   |  \value     \
   |   |   |###[ ControlParameters ]###
   |   |   |  type      = 104
   |   |   |  length    = 27
   |   |   |  \value     \
   |   |   |   |###[ Name ]###
   |   |   |   |  type      = 7
   |   |   |   |  length    = 12
   |   |   |   |  \value     \
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'test'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'ndn3'
   |   |   |   |###[ FaceId ]###
   |   |   |   |  type      = 105
   |   |   |   |  length    = 2
   |   |   |   |  value     = 311 (0x137)
   |   |   |   |###[ Origin ]###
   |   |   |   |  type      = 111
   |   |   |   |  length    = 1
   |   |   |   |  value     = 255 (0xFF)
   |   |   |   |###[ Cost ]###
   |   |   |   |  type      = 106
   |   |   |   |  length    = 1
   |   |   |   |  value     = 100 (0x64)
   |   |   |   |###[ Flags ]###
   |   |   |   |  type      = 108
   |   |   |   |  length    = 1
   |   |   |   |  value     = 1 (0x01)
   |   |###[ ParametersSha256DigestComponent ]###
   |   |  type      = 2
   |   |  length    = 32
   |   |  value     = b'\t/@\x96\x7f\x04u1~J\x7f"g-\xceKE\xbbg\xf4\x03\x11\xcd\xae\x86;M\xa3\xe7\x94\xbd\xbe'
   |###[ MetaInfo ]###
   |  type      = 20
   |  length    = 4
   |  \value     \
   |   |###[ FreshnessPeriod ]###
   |   |  type      = 25
   |   |  length    = 2
   |   |  value     = 1000 (0x3E8)
   |###[ Content ]###
   |  type      = 21
   |  length    = 43
   |  \value     \
   |   |###[ ControlResponse ]###
   |   |  type      = 101
   |   |  length    = 41
   |   |  \value     \
   |   |   |###[ StatusCode ]###
   |   |   |  type      = 102
   |   |   |  length    = 1
   |   |   |  value     = 200 (0xC8)
   |   |   |###[ StatusText ]###
   |   |   |  type      = 103
   |   |   |  length    = 7
   |   |   |  value     = b'Success'
   |   |   |###[ ControlParameters ]###
   |   |   |  type      = 104
   |   |   |  length    = 27
   |   |   |  \value     \
   |   |   |   |###[ Name ]###
   |   |   |   |  type      = 7
   |   |   |   |  length    = 12
   |   |   |   |  \value     \
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'test'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'ndn3'
   |   |   |   |###[ FaceId ]###
   |   |   |   |  type      = 105
   |   |   |   |  length    = 2
   |   |   |   |  value     = 311 (0x137)
   |   |   |   |###[ Origin ]###
   |   |   |   |  type      = 111
   |   |   |   |  length    = 1
   |   |   |   |  value     = 255 (0xFF)
   |   |   |   |###[ Cost ]###
   |   |   |   |  type      = 106
   |   |   |   |  length    = 1
   |   |   |   |  value     = 100 (0x64)
   |   |   |   |###[ Flags ]###
   |   |   |   |  type      = 108
   |   |   |   |  length    = 1
   |   |   |   |  value     = 1 (0x01)
   |###[ SignatureInfo ]###
   |  type      = 22
   |  length    = 3
   |  \value     \
   |   |###[ SignatureType ]###
   |   |  type      = 27
   |   |  length    = 1
   |   |  value     = 0 [DigestSha256]
   |###[ DigestSha256SignatureValue ]###
   |  type      = 23
   |  length    = 32
   |  \value     \
   |   |###[ DigestSha256 ]###
   |   |  value     = b'Z\xf1Y\xdfATg\xae#A\xe6"\xc5\xbd\x86\x12,\xe9\x13Z"\xcd\x01\x1d\xe2h\x0bg\xf5F\xc6h'

```

<br />

```Python
$ sn-nfdc strategy --list
###[ Interest ]###
  type      = 5
  length    = 45
  \value     \
   |###[ Name ]###
   |  type      = 7
   |  length    = 39
   |  \value     \
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 9
   |   |  value     = b'localhost'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 3
   |   |  value     = b'nfd'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 15
   |   |  value     = b'strategy-choice'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 4
   |   |  value     = b'list'
   |###[ CanBePrefix ]###
   |  type      = 33
   |  length    = 0
   |###[ MustBeFresh ]###
   |  type      = 18
   |  length    = 0

###[ Data ]###
  type      = 6
  length    = 343
  \value     \
   |###[ Name ]###
   |  type      = 7
   |  length    = 52
   |  \value     \
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 9
   |   |  value     = b'localhost'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 3
   |   |  value     = b'nfd'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 15
   |   |  value     = b'strategy-choice'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 4
   |   |  value     = b'list'
   |   |###[ Version Name Component ]###
   |   |  type      = 54
   |   |  length    = 8
   |   |  value     = 1745180811353 (0x19654E1E059)
   |   |###[ Segment Name Component ]###
   |   |  type      = 50
   |   |  length    = 1
   |   |  value     = 0 (0x00)
   |###[ MetaInfo ]###
   |  type      = 20
   |  length    = 9
   |  \value     \
   |   |###[ FreshnessPeriod ]###
   |   |  type      = 25
   |   |  length    = 2
   |   |  value     = 1000 (0x3E8)
   |   |###[ FinalBlockId ]###
   |   |  type      = 26
   |   |  length    = 3
   |   |  \value     \
   |   |   |###[ Segment Name Component ]###
   |   |   |  type      = 50
   |   |   |  length    = 1
   |   |   |  value     = 0 (0x00)
   |###[ Content ]###
   |  type      = 21
   |  length    = 237
   |  \value     \
   |   |###[ StrategyChoice ]###
   |   |  type      = 128
   |   |  length    = 47
   |   |  \value     \
   |   |   |###[ Name ]###
   |   |   |  type      = 7
   |   |   |  length    = 0
   |   |   |  \value     \
   |   |   |###[ Strategy ]###
   |   |   |  type      = 107
   |   |   |  length    = 43
   |   |   |  \value     \
   |   |   |   |###[ Name ]###
   |   |   |   |  type      = 7
   |   |   |   |  length    = 41
   |   |   |   |  \value     \
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 9
   |   |   |   |   |  value     = b'localhost'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 3
   |   |   |   |   |  value     = b'nfd'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 8
   |   |   |   |   |  value     = b'strategy'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 10
   |   |   |   |   |  value     = b'best-route'
   |   |   |   |   |###[ Version Name Component ]###
   |   |   |   |   |  type      = 54
   |   |   |   |   |  length    = 1
   |   |   |   |   |  value     = 5 (0x05)
   |   |###[ StrategyChoice ]###
   |   |  type      = 128
   |   |  length    = 57
   |   |  \value     \
   |   |   |###[ Name ]###
   |   |   |  type      = 7
   |   |   |  length    = 11
   |   |   |  \value     \
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 9
   |   |   |   |  value     = b'localhost'
   |   |   |###[ Strategy ]###
   |   |   |  type      = 107
   |   |   |  length    = 42
   |   |   |  \value     \
   |   |   |   |###[ Name ]###
   |   |   |   |  type      = 7
   |   |   |   |  length    = 40
   |   |   |   |  \value     \
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 9
   |   |   |   |   |  value     = b'localhost'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 3
   |   |   |   |   |  value     = b'nfd'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 8
   |   |   |   |   |  value     = b'strategy'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 9
   |   |   |   |   |  value     = b'multicast'
   |   |   |   |   |###[ Version Name Component ]###
   |   |   |   |   |  type      = 54
   |   |   |   |   |  length    = 1
   |   |   |   |   |  value     = 5 (0x05)
   |   |###[ StrategyChoice ]###
   |   |  type      = 128
   |   |  length    = 62
   |   |  \value     \
   |   |   |###[ Name ]###
   |   |   |  type      = 7
   |   |   |  length    = 16
   |   |   |  \value     \
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 3
   |   |   |   |  value     = b'ndn'
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 9
   |   |   |   |  value     = b'broadcast'
   |   |   |###[ Strategy ]###
   |   |   |  type      = 107
   |   |   |  length    = 42
   |   |   |  \value     \
   |   |   |   |###[ Name ]###
   |   |   |   |  type      = 7
   |   |   |   |  length    = 40
   |   |   |   |  \value     \
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 9
   |   |   |   |   |  value     = b'localhost'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 3
   |   |   |   |   |  value     = b'nfd'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 8
   |   |   |   |   |  value     = b'strategy'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 9
   |   |   |   |   |  value     = b'multicast'
   |   |   |   |   |###[ Version Name Component ]###
   |   |   |   |   |  type      = 54
   |   |   |   |   |  length    = 1
   |   |   |   |   |  value     = 5 (0x05)
   |   |###[ StrategyChoice ]###
   |   |  type      = 128
   |   |  length    = 63
   |   |  \value     \
   |   |   |###[ Name ]###
   |   |   |  type      = 7
   |   |   |  length    = 16
   |   |   |  \value     \
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 9
   |   |   |   |  value     = b'localhost'
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 3
   |   |   |   |  value     = b'nfd'
   |   |   |###[ Strategy ]###
   |   |   |  type      = 107
   |   |   |  length    = 43
   |   |   |  \value     \
   |   |   |   |###[ Name ]###
   |   |   |   |  type      = 7
   |   |   |   |  length    = 41
   |   |   |   |  \value     \
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 9
   |   |   |   |   |  value     = b'localhost'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 3
   |   |   |   |   |  value     = b'nfd'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 8
   |   |   |   |   |  value     = b'strategy'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 10
   |   |   |   |   |  value     = b'best-route'
   |   |   |   |   |###[ Version Name Component ]###
   |   |   |   |   |  type      = 54
   |   |   |   |   |  length    = 1
   |   |   |   |   |  value     = 5 (0x05)
   |###[ SignatureInfo ]###
   |  type      = 22
   |  length    = 3
   |  \value     \
   |   |###[ SignatureType ]###
   |   |  type      = 27
   |   |  length    = 1
   |   |  value     = 0 [DigestSha256]
   |###[ DigestSha256SignatureValue ]###
   |  type      = 23
   |  length    = 32
   |  \value     \
   |   |###[ DigestSha256 ]###
   |   |  value     = b'\x00\x9d\x01\x8c\xe1\xbc\xbbC\xe3\x95\x9a"\x8f>[\xdd\x9aL\xebBT\x9e\xd8iW\xb7\x1c.\xe8{\x96\x14'

```

```Python
$ sn-nfdc strategy --unset --prefix /test/ndn2 
###[ Interest ]###
  type      = 5
  length    = 256
  \value     \
   |###[ Name ]###
   |  type      = 7
   |  length    = 92
   |  \value     \
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 9
   |   |  value     = b'localhost'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 3
   |   |  value     = b'nfd'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 15
   |   |  value     = b'strategy-choice'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 5
   |   |  value     = b'unset'
   |   |###[ NameComponent (PktList) ]###
   |   |  type      = 8
   |   |  length    = 16
   |   |  \value     \
   |   |   |###[ ControlParameters ]###
   |   |   |  type      = 104
   |   |   |  length    = 14
   |   |   |  \value     \
   |   |   |   |###[ Name ]###
   |   |   |   |  type      = 7
   |   |   |   |  length    = 12
   |   |   |   |  \value     \
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'test'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'ndn2'
   |   |###[ ParametersSha256DigestComponent ]###
   |   |  type      = 2
   |   |  length    = 32
   |   |  value     = b'\xbcf,l`%S\xe5N[Tu\x97\xd5\x1a\xd5\xe4\xa09BK4"\xdc>\xb0\x85\x8e@\x112;'
   |###[ Nonce ]###
   |  type      = 10
   |  length    = 4
   |  value     = 0x150a0587
   |###[ ApplicationParameters ]###
   |  type      = 36
   |  length    = 0
   |  value     = b''
   |###[ InterestSignatureInfo ]###
   |  type      = 44
   |  length    = 79
   |  \value     \
   |   |###[ SignatureType ]###
   |   |  type      = 27
   |   |  length    = 1
   |   |  value     = 3 [SignatureSha256WithEcdsa]
   |   |###[ KeyLocator ]###
   |   |  type      = 28
   |   |  length    = 54
   |   |  \value     \
   |   |   |###[ Name ]###
   |   |   |  type      = 7
   |   |   |  length    = 52
   |   |   |  \value     \
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 9
   |   |   |   |  value     = b'localhost'
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 8
   |   |   |   |  value     = b'operator'
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 3
   |   |   |   |  value     = b'KEY'
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 8
   |   |   |   |  value     = b'\xe7B\x1bx"\x91\xed\xe0'
   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |  type      = 8
   |   |   |   |  length    = 4
   |   |   |   |  value     = b'self'
   |   |   |   |###[ Version Name Component ]###
   |   |   |   |  type      = 54
   |   |   |   |  length    = 8
   |   |   |   |  value     = 1699495954653 (0x18BB1DA48DD)
   |   |###[ SignatureNonce ]###
   |   |  type      = 38
   |   |  length    = 8
   |   |  value     = b'\xc5\x81\x94A\xb0\x17\xf2X'
   |   |###[ SignatureTime ]###
   |   |  type      = 40
   |   |  length    = 8
   |   |  value     = 1745180656959 [2025-04-20 13:24:16.959000]
   |###[ ECDSASignatureValue ]###
   |  type      = 46
   |  length    = 71
   |  \value     \
   |   |###[ ECDSASignature ]###
   |   |  r         = 0xf268ae30ec...8599595963 <ASN1_INTEGER[1096446636...7035329891]>
   |   |  s         = 0x70ef541bf6...2c7cf07aa2 <ASN1_INTEGER[5108189599...9264950946]>

###[ Data ]###
  type      = 6
  length    = 166
  \value     \
   |###[ Name ]###
   |  type      = 7
   |  length    = 92
   |  \value     \
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 9
   |   |  value     = b'localhost'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 3
   |   |  value     = b'nfd'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 15
   |   |  value     = b'strategy-choice'
   |   |###[ Name Component (String) ]###
   |   |  type      = 8
   |   |  length    = 5
   |   |  value     = b'unset'
   |   |###[ NameComponent (PktList) ]###
   |   |  type      = 8
   |   |  length    = 16
   |   |  \value     \
   |   |   |###[ ControlParameters ]###
   |   |   |  type      = 104
   |   |   |  length    = 14
   |   |   |  \value     \
   |   |   |   |###[ Name ]###
   |   |   |   |  type      = 7
   |   |   |   |  length    = 12
   |   |   |   |  \value     \
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'test'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'ndn2'
   |   |###[ ParametersSha256DigestComponent ]###
   |   |  type      = 2
   |   |  length    = 32
   |   |  value     = b'\xbcf,l`%S\xe5N[Tu\x97\xd5\x1a\xd5\xe4\xa09BK4"\xdc>\xb0\x85\x8e@\x112;'
   |###[ MetaInfo ]###
   |  type      = 20
   |  length    = 4
   |  \value     \
   |   |###[ FreshnessPeriod ]###
   |   |  type      = 25
   |   |  length    = 2
   |   |  value     = 1000 (0x3E8)
   |###[ Content ]###
   |  type      = 21
   |  length    = 25
   |  \value     \
   |   |###[ ControlResponse ]###
   |   |  type      = 101
   |   |  length    = 23
   |   |  \value     \
   |   |   |###[ StatusCode ]###
   |   |   |  type      = 102
   |   |   |  length    = 1
   |   |   |  value     = 200 (0xC8)
   |   |   |###[ StatusText ]###
   |   |   |  type      = 103
   |   |   |  length    = 2
   |   |   |  value     = b'OK'
   |   |   |###[ ControlParameters ]###
   |   |   |  type      = 104
   |   |   |  length    = 14
   |   |   |  \value     \
   |   |   |   |###[ Name ]###
   |   |   |   |  type      = 7
   |   |   |   |  length    = 12
   |   |   |   |  \value     \
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'test'
   |   |   |   |   |###[ Name Component (String) ]###
   |   |   |   |   |  type      = 8
   |   |   |   |   |  length    = 4
   |   |   |   |   |  value     = b'ndn2'
   |###[ SignatureInfo ]###
   |  type      = 22
   |  length    = 3
   |  \value     \
   |   |###[ SignatureType ]###
   |   |  type      = 27
   |   |  length    = 1
   |   |  value     = 0 [DigestSha256]
   |###[ DigestSha256SignatureValue ]###
   |  type      = 23
   |  length    = 32
   |  \value     \
   |   |###[ DigestSha256 ]###
   |   |  value     = b'\x9c\x87\x7f\xa4(\x12\x16{\x9aUS|`\xbb\xef\x19[\x7f\xe2K\xb3\xd0\xc1\xf7\xd3 \x9e\xac\xc8 \xc1N'

```


