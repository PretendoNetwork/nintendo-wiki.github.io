---
layout: post
toc: true
title: Monitoring Data
---

This protocol is used for the P2P monitoring server on the Switch. The messages are wrapped in unencrypted [Pia packets](/docs/pia/protocol) and sent to `g<game server id>-%.p.srv.nintendo.net` through UDP port 34343.

Wii U games send the monitoring data to the NEX server instead, through the [SendReport](/docs/nex/protocols/secure-connection#8-sendreport) method of the [secure connection protocol](/docs/nex/protocols/secure-connection).

The message payload is encoded as follows:

*Up to 5.6:*

| Offset | Size | Description                                                                                                         |
| ------ | ---- | ------------------------------------------------------------------------------------------------------------------- |
| 0x0    | 16   | [Monitoring data header](#monitoring-data-header)                                                                   |
| 0x10   |      | Payload, first zlib compressed, then encrypted with AES-ECB. The key is always: `901edf193dc5ef3c5290647bff20c385`. |

*5.7 and later:*

| Offset | Size | Description                                                                                                       |
| ------ | ---- | ----------------------------------------------------------------------------------------------------------------- |
| 0x0    | 16   | [Monitoring data header](#monitoring-data-header)                                                                 |
| 0x10   |      | Payload, first zlib compressed, then encrypted with AES-GCM. See [AES-GCM encryption](#aes-gcm-encryption) below. |
|        | 16   | AES-GCM authentication tag                                                                                        |

The content of the payload depends on the version number and data type in the monitoring data header.

| Data Type | Payload content                                                       |
| --------- | --------------------------------------------------------------------- |
| 0         | [Session begin monitoring content](#session-begin-monitoring-content) |
| 1         | [Session end monitoring data](#session-end-monitoring-data)           |
| 2         | [Session end monitoring data](#session-end-monitoring-data)           |

## AES-GCM Encryption
The key is chosen by the lower nybble of the encryption key id in the monitoring data header:

```
 0: c1d494af4a0a956c545d2e41fc1ceb24
 1: caf247fb40aa9655e58c2b02bff89e14
 2: bc6da24db8c7e22d2e3fdd97a2b5e3d2
 3: 3ef3a41d8a2e78518974679562afe5fa
 4: c0a02f90a2642ea6a64b199e01f46a57
 5: 9eb9c98fc889495c7056f2cd8015aac0
 6: 632acbe8c6c77246475b577b6b2d8c76
 7: 8dafd0578b2d3ff285e0292ce08d25a2
 8: cc082ad011d3c38c7b5ed28637f37c1c
 9: 6cd46046c71362116e36cb96c0098912
10: 54598618dc2a24474a9d5e80f783a145
11: f35eaa5cc8ebc1dc42ada6c3f4130556
12: 127d237973e98688548b5dac5eb6cc7b
13: f677ccf32241122aeeece3df23aaa736
14: d7cb0683d251030fc7613b4b2461224b
15: c587a79cbac8a2ddcee27409242a8702
```

The nonce is constructed as follows:

| Offset | Size | Description                       |
| ------ | ---- | --------------------------------- |
| 0x0    | 8    | Nonce from monitoring data header |
| 0x8    | 4    | Always `5bd085fa`                 |

## Version Number
Monitoring was added to Pia in version 3.4.

| Pia version | Monitoring version |
| ----------- | ------------------ |
| 3.4         | 1                  |
| 3.5         | 2                  |
| 3.6         | 3                  |
| 3.7         | 4                  |
| 3.8         | 5                  |
| 3.9         | 6                  |
| 3.10        | 7                  |
| 4.5         | 9                  |
| 4.6         | 10                 |
| 4.9         | 11                 |
| 4.10        | 12                 |
| 5.2 - 5.4   | 15                 |
| 5.5         | 16                 |
| 5.6         | 17                 |
| 5.7 - 5.9   | 18                 |
| 5.10 - 5.12 | 19                 |
| 5.14        | 20                 |
| 5.17 - 5.19 | 22                 |
| 5.20        | 23                 |
| 5.21 - 5.31 | 24                 |
| 5.32        | 25                 |

## Monitoring Data Header
As described above, the message payload starts with a monitoring data header. Each monitoring data structure starts with a monitoring data header as well. The flags field is always 0xFC in the first monitoring data header, and 0xFF in all other monitoring data headers.

In the first monitoring data header, the payload size indicates the size of the compressed payload *after* the payload has been decrypted. In all other monitoring data headers, the payload size indicates the size of the structure, including the monitoring data header itself.

| Offset | Size | Description                       |
| ------ | ---- | --------------------------------- |
| 0x0    | 1    | [Version number](#version-number) |
| 0x1    | 1    | Data type                         |
| 0x2    | 1    | Flags                             |
| 0x3    | 1    | Always 0xFF                       |
| 0x4    | 2    | Payload size                      |

*Up to 5.6:*

| Offset | Size | Description                |
| ------ | ---- | -------------------------- |
| 0x6    | 10   | Padding (filled with 0xFF) |

*5.7 and later:*

This part is only relevant in the first monitoring data header. In all other monitoring data headers, it is filled with 0xFF.

| Offset | Size | Description                       |
| ------ | ---- | --------------------------------- |
| 0x6    | 8    | AES-GCM nonce (random number)     |
| 0xE    | 1    | Encryption key id (random number) |
| 0xF    | 1    | Always 0xFF                       |

## Session Begin Monitoring Content
All fields are initialized to 0xFF.

Version 7:

| Offset | Size | Description                                                              |
| ------ | ---- | ------------------------------------------------------------------------ |
| 0x0    | 16   | [MonitoringDataHeader](#monitoring-data-header)                          |
| 0x10   | 4    | PIA version (u8: major, u8: minor, u8: patch, u8: padding)               |
| 0x1D   | 1    | Platform ID (2=3DS, 3=Wii U)                                             |
| 0x1E   | 1    | [Region ID](https://nintendo-wiki.pretendo.network/docs/misc/region-ids) |
| 0x37   | 4    | Uint32 client external IP address                                        |
| 0x3B   | 4    | Uint32 client local IP address                                           |
| 0x3F   | 1    | NAT Mapping                                                              |
| 0x40   | 1    | NAT Filtering                                                            |
| 0x42   | 1    | NAT type (flags)                                                         |
| 0x4D   | 4    | First 4 bytes of the MD5 hash of the connected users PID                 |
| 0x51   | 4    | Uint32 gathering ID                                                      |
| 0x55   | 4    | Game server ID                                                           |
| 0x19B  | 2    | Uint16 client external port                                              |
| 0x19D  | 2    | Uint16 client local port                                                 |

Version 11:

| Offset | Type                                                            | Description                                                                                                                     |
|--------|-----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| 0x0    | [MonitoringDataHeader](#monitoring-data-header)                 | Monitoring data header                                                                                                          |
| 0x10   | Uint32                                                          | PIA version (u8: major, u8: minor, u8: patch, u8: padding)                                                                      |
| 0x14   | Uint32                                                          | Unknown                                                                                                                         |
| 0x18   | Uint32                                                          | Unknown                                                                                                                         |
| 0x1C   | Uint8                                                           | Unknown                                                                                                                         |
| 0x1D   | Uint8                                                           | Platform ID (2=3DS, 3=Wii U)                                                                                                    |
| 0x1E   | Uint8                                                           | [Country ID](https://nintendo-wiki.pretendo.network/docs/misc/region-ids)                                                       |
| 0x1F   | Uint8                                                           | [Region ID](https://nintendo-wiki.pretendo.network/docs/misc/region-ids)                                                        |
| 0x20   | Uint32                                                          | Pia heap size                                                                                                                   |
| 0x24   | Uint32                                                          | Bitmask that describes which [protocols](/docs/pia/protocols) are created.                                                      |
| 0x28   | Uint32                                                          | Unknown                                                                                                                         |
| 0x2C   | Uint8                                                           | Unknown                                                                                                                         |
| 0x2D   | Uint32                                                          | Unknown                                                                                                                         |
| 0x31   | Uint32                                                          | Unknown                                                                                                                         |
| 0x35   | Uint8                                                           | Unknown                                                                                                                         |
| 0x36   | Uint8                                                           | Unknown                                                                                                                         |
| 0x37   | Uint32                                                          | External client address                                                                                                         |
| 0x3B   | Uint32                                                          | Local client address                                                                                                            |
| 0x3F   | Uint8                                                           | NAT Mapping                                                                                                                     |
| 0x40   | Uint8                                                           | NAT Filtering                                                                                                                   |
| 0x42   | Uint8                                                           | NAT type (flags)                                                                                                                |
| 0x43   | Uint16                                                          | Time used by NAT port detecter (milliseconds)                                                                                   |
| 0x45   | Uint16                                                          | Time required to resolve address of NAT check server (milliseconds)                                                             |
| 0x47   | Uint16                                                          | MTU size                                                                                                                        |
| 0x49   | Uint32                                                          | NEX connection ID of the user                                                                                                   |
| 0x4D   | Uint32                                                          | First 4 bytes of the MD5 hash of the connected users PID                                                                        |
| 0x51   | Uint32                                                          | Gathering ID                                                                                                                    |
| 0x55   | Uint32                                                          | Game server ID                                                                                                                  |
| 0x59   | Uint8                                                           | [Thread mode](#thread-mode)                                                                                                     |
| 0x5A   | Uint32                                                          | Unknown                                                                                                                         |
| 0x5E   | Uint32                                                          | Unknown                                                                                                                         |
| 0x62   | Uint32                                                          | Unknown                                                                                                                         |
| 0x66   | Uint8                                                           | Unknown                                                                                                                         |
| 0x67   | Uint8                                                           | Unknown                                                                                                                         |
| 0x68   | Uint16                                                          | Maximum number of stations allowed                                                                                              |
| 0x6A   | Uint32                                                          | Unknown                                                                                                                         |
| 0x6E   | Uint32                                                          | Unknown                                                                                                                         |
| 0x72   | Uint32                                                          | Number of reliable send data blocks                                                                                             |
| 0x76   | Uint32                                                          | Number of reliable receive data blocks                                                                                          |
| 0x7A   | Uint32                                                          | Unknown                                                                                                                         |
| 0x7E   | Uint16                                                          | Lowest RTT average (milliseconds)                                                                                               |
| 0x80   | Uint32                                                          | First 4 bytes of the MD5 hash of the PID of the user with the lowest RTT average                                                |
| 0x84   | Uint16                                                          | Highest RTT average (milliseconds)                                                                                              |
| 0x86   | Uint32                                                          | First 4 bytes of the MD5 hash of the PID of the user with the highest RTT average                                               |
| 0x8A   | Uint16                                                          | Relay route RTT max                                                                                                             |
| 0x8C   | Uint16                                                          | Relay route num max                                                                                                             |
| 0x8E   | Uint16                                                          | Maximum silence time (milliseconds)                                                                                             |
| 0x90   | Uint32                                                          | Keep alive interval (milliseconds)                                                                                              |
| 0x94   | Uint8                                                           | Unknown                                                                                                                         |
| 0x95   | Uint8                                                           | Unknown                                                                                                                         |
| 0x96   | Uint8                                                           | Unknown                                                                                                                         |
| 0x97   | Uint32                                                          | Join mesh job result                                                                                                            |
| 0x9B   | Uint32                                                          | Processing time of join mesh (milliseconds)                                                                                     |
| 0x9F   | Uint8                                                           | PID relay count (1)                                                                                                             |
| 0xA0   | Uint32[23]                                                      | First 4 bytes of the MD5 hash of the PIDs in relay (1)                                                                          |
| 0xFC   | Uint8                                                           | PID relay count (2)                                                                                                             |
| 0xFD   | Uint32[12]                                                      | First 4 bytes of the MD5 hash of the PIDs in relay (2)                                                                          |
| 0x12D  | Uint16                                                          | Unknown                                                                                                                         |
| 0x12F  | Uint8                                                           | Unknown                                                                                                                         |
| 0x130  | Uint8                                                           | Unknown                                                                                                                         |
| 0x131  | Uint32                                                          | Unknown                                                                                                                         |
| 0x135  | Uint16                                                          | Unknown                                                                                                                         |
| 0x137  | Uint16                                                          | Unknown                                                                                                                         |
| 0x139  | Uint16                                                          | Unknown                                                                                                                         |
| 0x13B  | Uint16                                                          | Unknown                                                                                                                         |
| 0x13D  | Uint16                                                          | Unknown                                                                                                                         |
| 0x13F  | Uint16                                                          | Unknown                                                                                                                         |
| 0x141  | Uint16                                                          | Unknown                                                                                                                         |
| 0x143  | Uint16                                                          | Unknown                                                                                                                         |
| 0x145  | Uint8                                                           | Unknown                                                                                                                         |
| 0x146  | Uint16                                                          | Unknown                                                                                                                         |
| 0x148  | Uint16                                                          | External client port                                                                                                            |
| 0x14A  | Uint16                                                          | Local client port                                                                                                               |
| 0x14C  | Uint8                                                           | Debug mode (Wii U: OSGetSecurityLevel() != 0)                                                                                   |
| 0x14D  | Uint32                                                          | First 4 bytes of the MD5 hash of the gathering hosts PID                                                                        |
| 0x151  | Uint8                                                           | Unknown                                                                                                                         |
| 0x152  | Uint8                                                           | Unknown                                                                                                                         |
| 0x153  | Uint8                                                           | Unknown                                                                                                                         |
| 0x154  | Uint8                                                           | Unknown                                                                                                                         |
| 0x155  | Uint32                                                          | Joint session gathering ID                                                                                                      |
| 0x159  | Uint32                                                          | Unknown                                                                                                                         |
| 0x15D  | Uint32                                                          | Unknown                                                                                                                         |
| 0x161  | Uint8                                                           | Unknown                                                                                                                         |
| 0x162  | Uint8                                                           | Unknown                                                                                                                         |
| 0x163  | Uint32                                                          | Processing time of join session (milliseconds)                                                                                  |
| 0x167  | Uint32                                                          | Session game mode                                                                                                               |
| 0x16B  | Uint32[6]                                                       | Session attributes                                                                                                              |
| 0x183  | Uint16                                                          | Session max participants                                                                                                        |
| 0x185  | [NexSessionSearchCriteria](#nexsessionsearchcriteria)           | Search criteria 1                                                                                                               |
| 0x1A2  | [NexSessionSearchCriteria](#nexsessionsearchcriteria)           | Search criteria 2                                                                                                               |
| 0x1BF  | Uint8                                                           | Joint number of session join attempts                                                                                           |
| 0x1C0  | Uint8                                                           | Joint session max participants                                                                                                  |
| 0x1C1  | Uint8                                                           | Session extra participants                                                                                                      |
| 0x1C2  | [NexSessionSearchCriteriaExtra](#nexsessionsearchcriteriaextra) | Extra search criteria 1                                                                                                         |
| 0x1D0  | [NexSessionSearchCriteriaExtra](#nexsessionsearchcriteriaextra) | Extra search criteria 2                                                                                                         |
| 0x1DE  | Uint8                                                           | Number of NAT traversal failures                                                                                                |
| 0x1DF  | Uint8                                                           | Number of session join failures because the connection with the host could not be established                                   |
| 0x1E0  | Uint8                                                           | Number of session join failures because of an error receiving the join response                                                 |
| 0x1E1  | Uint8                                                           | Number of session join failures because the session started host migration before a connection was established with all clients |
| 0x1E2  | Uint8                                                           | Number of session join failures because the session started host migration before the join was completed                        |
| 0x1E3  | Uint8                                                           | Number of session join failures because the join request was denied                                                             |
| 0x1E4  | Uint32                                                          | Unknown                                                                                                                         |
| 0x1E8  | Uint32                                                          | Unknown                                                                                                                         |
| 0x1EC  | Uint8                                                           | Unknown                                                                                                                         |
| 0x1ED  | Uint8                                                           | Joint sequence                                                                                                                  |
| 0x1EE  | Uint8                                                           | Join sequence                                                                                                                   |
| 0x1EF  | Uint8                                                           | Join auto matchmake sequence                                                                                                    |
| 0x1F0  | Uint8                                                           | Join mesh sequence                                                                                                              |
| 0x1F1  | Uint8                                                           | Unknown                                                                                                                         |
| 0x1F2  | Uint16                                                          | Bandwidth checker ip packet size                                                                                                |
| 0x1F4  | Uint16                                                          | Unknown send packet num in measuring time                                                                                       |
| 0x1F6  | Uint16                                                          | Bandwidth checker bps                                                                                                           |
| 0x1FA  | Uint32                                                          | Unknown                                                                                                                         |
| 0x1FE  | Uint16                                                          | NAT property detection count                                                                                                    |
| 0x200  | Uint16                                                          | NAT port detection count                                                                                                        |
| 0x202  | Uint32[23]                                                      | NEX connection IDs in the session                                                                                               |
| 0x25E  | Uint32[23]                                                      | First 4 bytes of the MD5 hash of the PIDs in the session                                                                        |
| 0x2BA  | Uint32[23]                                                      | Unknown (milliseconds)                                                                                                          |
| 0x316  | Uint32[23]                                                      | Unknown (milliseconds)                                                                                                          |
| 0x372  | Uint32[23]                                                      | NAT traversal results                                                                                                           |
| 0x3CE  | Uint8[23]                                                       | Unknown                                                                                                                         |
| 0x3E5  | Uint8[23]                                                       | Port spray counts                                                                                                               |
| 0x3FC  | Uint8[23]                                                       | Unknown                                                                                                                         |
| 0x414  | Uint8[23]                                                       | Unknown                                                                                                                         |
| 0x42A  | Uint8                                                           | Start NAT traversal result                                                                                                      |
| 0x42B  | Uint8                                                           | Complete NAT traversal result                                                                                                   |
| 0x42C  | Uint16                                                          | Update target address count                                                                                                     |
| 0x42E  | Uint16                                                          | NAT traversal success count                                                                                                     |
| 0x430  | Uint16                                                          | NAT traversal failure count                                                                                                     |
| 0x432  | Uint8[8]                                                        | Unknown                                                                                                                         |

Version 18:

| Type                                                            | Description                                                                                                                     |
| --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| [MonitoringDataHeader](#monitoring-data-header)                 | Monitoring data header                                                                                                          |
| Uint32                                                          | Pia version (u8: major, u8: minor, u8: patch, u8: padding)                                                                      |
| Uint32                                                          | SDK version                                                                                                                     |
| Uint32                                                          | NEX version (u8: major, u8: minor, u8: patch, u8: padding)                                                                      |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Platform id (3=Wii U, 4=Switch)                                                                                                 |
| Uint8                                                           | Language                                                                                                                        |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint32                                                          | Pia heap size                                                                                                                   |
| Uint32                                                          | Bitmask that describes which [protocols](/docs/pia/protocols) are created.                                                      |
| Uint64                                                          | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint32                                                          | External client address                                                                                                         |
| Uint32                                                          | Local client address                                                                                                            |
| Uint8                                                           | NAT mapping                                                                                                                     |
| Uint8                                                           | NAT filtering                                                                                                                   |
| Uint8                                                           | NAT port increment                                                                                                              |
| Uint8                                                           | NAT mode flags                                                                                                                  |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Time required to resolve address of NAT check server (milliseconds)                                                             |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | MD5 hash of pid                                                                                                                 |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Game server id                                                                                                                  |
| Uint8                                                           | [Thread mode](#thread-mode)                                                                                                     |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Relay route rtt max                                                                                                             |
| Uint16                                                          | Relay route num max                                                                                                             |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | External client port                                                                                                            |
| Uint16                                                          | Local client port                                                                                                               |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint32                                                          | MD5 hash of pid of session host                                                                                                 |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint32                                                          | Joint session gathering id                                                                                                      |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint32                                                          | Time required to create or join matchmake session (milliseconds)                                                                |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| [NexSessionSearchCriteria](#nexsessionsearchcriteria)           | Search criteria 1                                                                                                               |
| [NexSessionSearchCriteria](#nexsessionsearchcriteria)           | Search criteria 2                                                                                                               |
| Uint8                                                           | Number of session join attempts                                                                                                 |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| [NexSessionSearchCriteriaExtra](#nexsessionsearchcriteriaextra) | Extra search criteria 1                                                                                                         |
| [NexSessionSearchCriteriaExtra](#nexsessionsearchcriteriaextra) | Extra search criteria 2                                                                                                         |
| Uint8                                                           | Number of NAT traversal failures                                                                                                |
| Uint8                                                           | Number of session join failures because the connection with the host could not be established                                   |
| Uint8                                                           | Number of session join failures because of an error receiving the join response                                                 |
| Uint8                                                           | Number of session join failures because the session started host migration before a connection was established with all clients |
| Uint8                                                           | Number of session join failures because the session started host migration before the join was completed                        |
| Uint8                                                           | Number of session join failures because the join request was denied                                                             |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint32                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint32[75]                                                      | Unknown                                                                                                                         |
| Uint8[60]                                                       | Unknown                                                                                                                         |
| Uint8                                                           | Last [result](#nat-traversal-result) of starting NAT traversal                                                                  |
| Uint8                                                           | Last [result](#nat-traversal-result) of completing NAT traversal                                                                |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint16                                                          | Time required to resolve address of monitoring server (milliseconds)                                                            |
| Uint16                                                          | Unknown                                                                                                                         |
| Uint8[15]                                                       | Unknown                                                                                                                         |
| Uint32                                                          | NAT relay server address                                                                                                        |
| Uint16                                                          | NAT relay server port                                                                                                           |
| Uint8[67]                                                       | Unknown                                                                                                                         |
| Uint8[150]                                                      | Unknown                                                                                                                         |
| Uint8[150]                                                      | Unknown                                                                                                                         |
| Uint8[32]                                                       | Unknown                                                                                                                         |
| Uint8[150]                                                      | Unknown                                                                                                                         |
| Uint8[176]                                                      | Unknown                                                                                                                         |
| Uint8[300]                                                      | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint8                                                           | Unknown                                                                                                                         |
| Uint32                                                          | Always `PiaM`                                                                                                                   |

### NexSessionSearchCriteria

Version 11:

| Type       | Description                                                                                           |
|------------|-------------------------------------------------------------------------------------------------------|
| Uint32     | Game mode                                                                                             |
| Uint8      | Min participants num range (min)                                                                      |
| Uint8      | Min participants num range (max)                                                                      |
| Uint8      | Max participants num range (min)                                                                      |
| Uint8      | Max participants num range (max)                                                                      |
| Uint8 (x6) | Attribute array size                                                                                  |
| Uint8 (x6) | Attribute range min                                                                                   |
| Uint8 (x6) | Attribute range max                                                                                   |
| Uint8      | Matchmake system type                                                                                 |
| Uint8      | Flags:<br>1: exclude user password set<br>2: exclude non host pid<br>4: opened only<br>8: vacant only |
| Uint8      | Selection method                                                                                      |

Version 18:

| Type        | Description                                                                                           |
| ----------- | ----------------------------------------------------------------------------------------------------- |
| Uint32      | Game mode                                                                                             |
| Uint8       | Min participants num range (min)                                                                      |
| Uint8       | Min participants num range (max)                                                                      |
| Uint8       | Max participants num range (min)                                                                      |
| Uint8       | Max participants num range (max)                                                                      |
| Uint8 (x6)  | Attribute array size                                                                                  |
| Uint32 (x6) | Attribute range min                                                                                   |
| Uint32 (x6) | Attribute range max                                                                                   |
| Uint8       | Matchmake system type                                                                                 |
| Uint8       | Flags:<br>1: exclude user password set<br>2: exclude non host pid<br>4: opened only<br>8: vacant only |
| Uint8       | Selection method                                                                                      |

### NexSessionSearchCriteriaExtra

| Type   | Description        |
| ------ | ------------------ |
| Uint32 | Rating value       |
| Uint32 | Violation rate     |
| Uint32 | Disconnection rate |
| Uint8  | Unknown            |
| Uint8  | Use geo ip         |

### Thread Mode

| Mode | Description                       |
| ---- | --------------------------------- |
| 0    | ThreadModeUndefined               |
| 1    | ThreadModeSafeTransportBuffer     |
| 2    | ThreadModeUnsafeTransportBuffer   |
| 3    | ThreadModeUnsafeUser              |
| 4    | ThreadModeSafeUser                |
| 5    | ThreadModeInternal                |
| 6    | ThreadModeInternalTransportBuffer |

### NAT Traversal Result

| Value | Description                |
| ----- | -------------------------- |
| 0     | Reliable                   |
| 1     | Unreliable                 |
| 2     | Failure or very unreliable |

## Session End Monitoring Data

| Offset | Size | Description                                       |
| ------ | ---- | ------------------------------------------------- |
| 0x0    | 16   | [Monitoring data header](#monitoring-data-header) |
| ...    | ...  | ...                                               |
