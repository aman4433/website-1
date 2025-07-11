---
title: Configuration options
weight: 4050
description: etcd configuration files, flags, and environment variables
---

You can configure etcd through the following:

- **[Command-line flags](#command-line-flags)**
- **Environment variables**: every flag has a corresponding environment variable
  that has the same name but is prefixed with `ETCD_` and formatted in all caps and
  [snake case][]. For example, `--some-flag` would be `ETCD_SOME_FLAG`.
- **[Configuration file](#configuration-file)**

{{% alert color="warning" %}}
<i class="fas fa-exclamation-triangle mr-1"></i> **Caution**: If you mix-and-match configuration options, then the following
rules apply.

- Command-line flags take precedence over environment variables.
- If you provide a _configuration file_ all command-line flags and environment variables are **ignored**.
{{% /alert %}}

## Command-line flags

Flags are presented below using the format `--flag-name DEFAULT_VALUE`.

The list of flags provided below may not be up-to-date due to ongoing development changes. For the latest available flags, run `etcd --help` or refer to the [etcd help][].

{{% alert color="info" %}}
  **Note**: For details concerning new, updated, and deprecated {{< param version >}} flags,
  see [CHANGELOG-{{< psubstr version 1 >}}.md][changelog].

  [changelog]: https://github.com/etcd-io/etcd/blob/main/CHANGELOG/CHANGELOG-{{< psubstr version 1 >}}.md
{{% /alert %}}

### Member

```nocode
--name 'default'
  Human-readable name for this member.
--data-dir '${name}.etcd'
  Path to the data directory.
--wal-dir ''
  Path to the dedicated wal directory.
--snapshot-count '10000'
  Number of committed transactions to trigger a snapshot to disk.
--heartbeat-interval '100'
  Time (in milliseconds) of a heartbeat interval.
--election-timeout '1000'
  Time (in milliseconds) for an election to timeout. See tuning documentation for details.
--initial-election-tick-advance 'true'
  Whether to fast-forward initial election ticks on boot for faster election.
--listen-peer-urls 'http://localhost:2380'
  List of URLs to listen on for peer traffic.
--listen-client-urls 'http://localhost:2379'
  List of URLs to listen on for client grpc traffic and http as long as --listen-client-http-urls is not specified.
--listen-client-http-urls ''
  List of URLs to listen on for http only client traffic. Enabling this flag removes http services from --listen-client-urls.
--max-snapshots '5'
  Maximum number of snapshot files to retain (0 is unlimited).
--max-wals '5'
  Maximum number of wal files to retain (0 is unlimited).
--quota-backend-bytes '0'
  Raise alarms when backend size exceeds the given quota (0 defaults to low space quota).
--backend-bbolt-freelist-type 'map'
  BackendFreelistType specifies the type of freelist that boltdb backend uses(array and map are supported types).
--backend-batch-interval ''
  BackendBatchInterval is the maximum time before commit the backend transaction.
--backend-batch-limit '0'
  BackendBatchLimit is the maximum operations before commit the backend transaction.
--max-txn-ops '128'
  Maximum number of operations permitted in a transaction.
--max-request-bytes '1572864'
  Maximum client request size in bytes the server will accept.
--grpc-keepalive-min-time '5s'
  Minimum duration interval that a client should wait before pinging server.
--grpc-keepalive-interval '2h'
  Frequency duration of server-to-client ping to check if a connection is alive (0 to disable).
--grpc-keepalive-timeout '20s'
  Additional duration of wait before closing a non-responsive connection (0 to disable).
--socket-reuse-port 'false'
  Enable to set socket option SO_REUSEPORT on listeners allowing rebinding of a port already in use.
--socket-reuse-address 'false'
  Enable to set socket option SO_REUSEADDR on listeners allowing binding to an address in TIME_WAIT state.
```
### Clustering

```nocode
--initial-advertise-peer-urls 'http://localhost:2380'
  List of this member's peer URLs to advertise to the rest of the cluster.
--initial-cluster 'default=http://localhost:2380'
  Initial cluster configuration for bootstrapping.
--initial-cluster-state 'new'
  Initial cluster state ('new' or 'existing').
--initial-cluster-token 'etcd-cluster'
  Initial cluster token for the etcd cluster during bootstrap.
  Specifying this can protect you from unintended cross-cluster interaction when running multiple clusters.
--advertise-client-urls 'http://localhost:2379'
  List of this member's client URLs to advertise to the public.
  The client URLs advertised should be accessible to machines that talk to etcd cluster. etcd client libraries parse these URLs to connect to the cluster.
--discovery ''
  Discovery URL used to bootstrap the cluster.
--discovery-fallback 'proxy'
  Expected behavior ('exit' or 'proxy') when discovery services fails.
  "proxy" supports v2 API only.
--discovery-proxy ''
  HTTP proxy to use for traffic to discovery service.
--discovery-srv ''
  DNS srv domain used to bootstrap the cluster.
--discovery-srv-name ''
  Suffix to the dns srv name queried when bootstrapping.
--strict-reconfig-check 'true'
  Reject reconfiguration requests that would cause quorum loss.
--pre-vote 'true'
  Enable the raft Pre-Vote algorithm to prevent disruption when a node that has been partitioned away rejoins the cluster.
--auto-compaction-retention '0'
  Auto compaction retention length. 0 means disable auto compaction.
--auto-compaction-mode 'periodic'
  Interpret 'auto-compaction-retention' one of: periodic|revision. 'periodic' for duration based retention, defaulting to hours if no time unit is provided (e.g. '5m'). 'revision' for revision number based retention.
--enable-v2 'false'
  Accept etcd V2 client requests. Deprecated and to be decommissioned in v3.6.
--v2-deprecation 'not-yet'
  Phase of v2store deprecation. Allows to opt-in for higher compatibility mode.
  Supported values:
    'not-yet'                // Issues a warning if v2store have meaningful content (default in v3.5)
    'write-only'             // Custom v2 state is not allowed (planned default in v3.6)
    'write-only-drop-data'   // Custom v2 state will get DELETED !
    'gone'                   // v2store is not maintained any longer. (planned default in v3.7)
```
### Security

```nocode
--cert-file ''
  Path to the client server TLS cert file.
--key-file ''
  Path to the client server TLS key file.
--client-cert-auth 'false'
  Enable client cert authentication.
  It's recommended to enable client cert authentication to prevent attacks from unauthenticated clients (e.g. CVE-2023-44487), especially when running etcd as a public service.
--client-crl-file ''
  Path to the client certificate revocation list file.
--client-cert-allowed-hostname ''
  Allowed TLS hostname for client cert authentication.
--trusted-ca-file ''
  Path to the client server TLS trusted CA cert file.
  Note setting this parameter will also automatically enable client cert authentication no matter what value is set for `--client-cert-auth`.
--auto-tls 'false'
  Client TLS using generated certificates.
--peer-cert-file ''
  Path to the peer server TLS cert file.
--peer-key-file ''
  Path to the peer server TLS key file.
--peer-client-cert-auth 'false'
  Enable peer client cert authentication.
  It's recommended to enable peer client cert authentication to prevent attacks from unauthenticated forged peers (e.g. CVE-2023-44487).
--peer-trusted-ca-file ''
  Path to the peer server TLS trusted CA file.
--peer-cert-allowed-cn ''
  Required CN for client certs connecting to the peer endpoint.
--peer-cert-allowed-hostname ''
  Allowed TLS hostname for inter peer authentication.
--peer-auto-tls 'false'
  Peer TLS using self-generated certificates if --peer-key-file and --peer-cert-file are not provided.
--self-signed-cert-validity '1'
  The validity period of the client and peer certificates that are automatically generated by etcd when you specify ClientAutoTLS and PeerAutoTLS, the unit is year, and the default is 1.
--peer-crl-file ''
  Path to the peer certificate revocation list file.
--cipher-suites ''
  Comma-separated list of supported TLS cipher suites between client/server and peers (empty will be auto-populated by Go).
--cors '*'
  Comma-separated whitelist of origins for CORS, or cross-origin resource sharing, (empty or * means allow all).
--host-whitelist '*'
  Acceptable hostnames from HTTP client requests, if server is not secure (empty or * means allow all).
--tls-min-version 'TLS1.2'
  Minimum TLS version supported by etcd.
--tls-max-version ''
  Maximum TLS version supported by etcd (empty will be auto-populated by Go).
```
### Auth

```nocode
--auth-token 'simple'
  Specify a v3 authentication token type and its options ('simple' or 'jwt').
--bcrypt-cost 10
  Specify the cost / strength of the bcrypt algorithm for hashing auth passwords. Valid values are between 4 and 31.
--auth-token-ttl 300
  Time (in seconds) of the auth-token-ttl.
```
### Profiling and monitoring

```nocode
--enable-pprof 'false'
  Enable runtime profiling data via HTTP server. Address is at client URL + "/debug/pprof/"
--metrics 'basic'
  Set level of detail for exported metrics, specify 'extensive' to include server side grpc histogram metrics.
--listen-metrics-urls ''
  List of URLs to listen on for the metrics and health endpoints.
```
### Logging

```nocode
--logger 'zap'
  Currently only supports 'zap' for structured logging.
--log-outputs 'default'
  Specify 'stdout' or 'stderr' to skip journald logging even when running under systemd, or list of comma separated output targets.
--log-level 'info'
  Configures log level. Only supports debug, info, warn, error, panic, or fatal.
--log-format 'json'
  Configures log format. Only supports json, console.
--enable-log-rotation 'false'
  Enable log rotation of a single log-outputs file target.
--log-rotation-config-json '{"maxsize": 100, "maxage": 0, "maxbackups": 0, "localtime": false, "compress": false}'
  Configures log rotation if enabled with a JSON logger config. MaxSize(MB), MaxAge(days,0=no limit), MaxBackups(0=no limit), LocalTime(use computers local time), Compress(gzip)".
```
{{% alert color="info" %}}
⚠️ **Note**: Several `--experimental-*` flags have been promoted or renamed in v3.7.
Be sure to replace deprecated flags with their stable counterparts listed below.
{{% /alert %}}

### Distributed tracing

```nocode
--enable-distributed-tracing 'false'
  Enable distributed tracing.
--distributed-tracing-address 'localhost:4317'
  Distributed tracing collector address.
--distributed-tracing-service-name 'etcd'
  Distributed tracing service name, must be the same across all etcd instances.
--distributed-tracing-instance-id ''
  Distributed tracing instance ID, must be unique for each etcd instance.
--distributed-tracing-sampling-rate '0'
  Number of samples to collect per million spans for distributed tracing.
```
### v2 Proxy

{{% alert color="warning" %}}
**<i class="fas fa-exclamation-triangle mr-1"></i> Note**: flags will be deprecated in v3.6.
{{% /alert %}}


```nocode
--proxy 'off'
  Proxy mode setting ('off', 'readonly' or 'on').
--proxy-failure-wait 5000
  Time (in milliseconds) an endpoint will be held in a failed state.
--proxy-refresh-interval 30000
  Time (in milliseconds) of the endpoints refresh interval.
--proxy-dial-timeout 1000
  Time (in milliseconds) for a dial to timeout.
--proxy-write-timeout 5000
  Time (in milliseconds) for a write to timeout.
--proxy-read-timeout 0
  Time (in milliseconds) for a read to timeout.
```
### Features

```nocode
--corrupt-check-time '0s'
  Duration of time between cluster corruption check passes.
--compact-hash-check-time '1m'
  Duration of time between leader checks followers compaction hashes.
--compaction-batch-limit 1000
  Maximum revisions deleted in each compaction batch during compaction.
--peer-skip-client-san-verification 'false'
  Skip verification of the SAN field in client certificate for peer connections.
--watch-progress-notify-interval '10m'
  Duration of periodical watch progress notification.
--warning-unary-request-duration '300ms'
  Set the threshold after which a unary request triggers a warning.
--warning-apply-duration '100ms'
  Warning is generated if request processing exceeds this duration.
--bootstrap-defrag-threshold-megabytes
  Enable defragmentation during server bootstrap if the freed disk space exceeds the given threshold.
--snapshot-catchup-entries
  Number of entries for a slow follower to catch up after raft log compaction.
--compaction-sleep-interval
  Sleep interval between each compaction batch.
--downgrade-check-time
  Duration of time between downgrade status checks.
```
### Unsafe features

{{% alert color="warning" %}}
**<i class="fas fa-exclamation-triangle mr-1"></i> Warning**: using unsafe features may break the guarantees given by the consensus protocol!
{{% /alert %}}

```nocode
--force-new-cluster 'false'
  Force to create a new one-member cluster.
--unsafe-no-fsync 'false'
  Disables fsync, unsafe, will cause data loss.
```

## Configuration file

An etcd configuration file consists of a YAML map whose keys are command-line
flag names and values are the flag values.
In order to use this file, specify the file path as a value to the `--config-file` flag or `ETCD_CONFIG_FILE` environment variable.

For an example, see the [etcd.conf.yml sample][].

[etcd help]: https://github.com/etcd-io/etcd/blob/main/server/etcdmain/help.go
[etcd.conf.yml sample]: https://github.com/etcd-io/etcd/blob/main/etcd.conf.yml.sample
[snake case]: https://en.wikipedia.org/wiki/Snake_case
