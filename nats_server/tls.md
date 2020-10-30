## TLS Configuration

The NATS server uses modern TLS semantics to encrypt client, route, and monitoring connections.
Server configuration revolves around a `tls` map, which has the following properties:

| Property | Description |
| :------  | :---- |
| `ca_file` | TLS certificate authority file. |
| `cert_file` | TLS certificate file. |
| `cipher_suites` | When set, only the specified TLS cipher suites will be allowed. Values must match the golang version used to build the server.  |
| `curve_preferences` | List of TLS cipher curves to use in order. |
| `insecure` | Skip certificate verification. |
| `key_file` | TLS certificate key file. |
| `timeout` | TLS handshake timeout in fractional seconds. Default set to 2 seconds. |
| `verify_and_map` | If `true`, require and verify client certificates and map certificate values for authentication purposes. |
| `verify` | If `true`, require and verify client certificates. |

The simplest configuration:
```
tls: {
  cert_file: "./server-cert.pem"
  key_file: "./server-key.pem"
}
```
Or by using [server options](./flags.md#tls-options):
```
> nats-server --tls --tlscert=./server-cert.pem --tlskey=./server-key.pem
[21417] 2019/05/16 11:21:19.801539 [INF] Starting nats-server version 2.0.0
[21417] 2019/05/16 11:21:19.801621 [INF] Git commit [not set]
[21417] 2019/05/16 11:21:19.801777 [INF] Listening for client connections on 0.0.0.0:4222
[21417] 2019/05/16 11:21:19.801782 [INF] TLS required for client connections
[21417] 2019/05/16 11:21:19.801785 [INF] Server id is ND6ZZDQQDGKYQGDD6QN2Y26YEGLTH6BMMOJZ2XJB2VASPVII3XD6RFOQ
[21417] 2019/05/16 11:21:19.801787 [INF] Server is ready
```

Notice that the log indicates that the client connections will be required to use TLS. If you run the server in Debug mode with `-D` or `-DV`, the logs will show the cipher suite selection for each connected client:

```
[22242] 2019/05/16 11:22:20.216322 [DBG] 127.0.0.1:51383 - cid:1 - Client connection created
[22242] 2019/05/16 11:22:20.216539 [DBG] 127.0.0.1:51383 - cid:1 - Starting TLS client connection handshake
[22242] 2019/05/16 11:22:20.367275 [DBG] 127.0.0.1:51383 - cid:1 - TLS handshake complete
[22242] 2019/05/16 11:22:20.367291 [DBG] 127.0.0.1:51383 - cid:1 - TLS version 1.2, cipher suite TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
```

When a `tls` section is specified at the root of the configuration, it also affects the monitoring port if `https_port` option is specified. Other sections such as `cluster` can specify a `tls` block.


### TLS Timeout

The `timeout` setting enables you to control the amount of time that a client is allowed to upgrade its connection to tls. If your clients are experiencing disconnects during TLS handshake, you'll want to increase the value, however, if you do be aware that an extended `timeout` exposes your server to attacks where a client doesn't upgrade to TLS and thus consumes resources. Conversely, if you reduce the TLS `timeout` too much, you are likely to experience handshake errors.


```
tls: {
  cert_file: "./server-cert.pem"
  key_file: "./server-key.pem"
  # clients will fail to connect (value is too low)
  timeout: 0.0001
}
```

### Certificate Authorities

The `ca_file` file should contain one or more Certificate Authorities in PEM
format, in a bundle.  This is a common format.

When a certificate is issued, it is often accompanied by a copy of the
intermediate certificate used to issue it.  This is useful for validating that
certificate.  It is not necessarily a good choice as the only CA suitable for
use in verifying other certificates a server may see.

Do consider though that organizations issuing certificates will change the
intermediate they use.  For instance, a CA might issue intermediates in pairs,
with an active and a standby, and reserve the right to switch to the standby
without notice.  You probably would want to trust _both_ of those for the
`ca_file` directive, to be prepared for such a day, and then after the first
CA has been compromised you can remove it.  This way the roll from one CA to
another will not break your NATS server deployment.
