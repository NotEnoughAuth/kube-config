Need to create a secret that will hold the private key for the server

apiVersion: v1
kind: Secret
metadata:
name: ssh-key
namespace: acme
data:
id_rsa: {{ secret_value_base64_encoded }} #id_rsa is the name of the file

Also need to create a secret that will hold the configuration file for the server

apiVersion: v1
kind: Secret
metadata:
name: mysecret
type: Opaque
stringData:
config.yaml: |
version: 2
global: # -- **REQUIRED** Servername for this Dendrite deployment.
server_name: ""

      # -- The private key to use. (**NOTE**: This is overriden in Helm)
      private_key: /etc/dendrite/secrets/signing.key

      # -- The server name to delegate server-server communications to, with optional port
      # e.g. localhost:443
      well_known_server_name: ""

      # -- The server name to delegate client-server communications to, with optional port
      # e.g. localhost:443
      well_known_client_name: ""

      # -- Lists of domains that the server will trust as identity servers to verify third
      # party identifiers such as phone numbers and email addresses.
      trusted_third_party_id_servers:
        - matrix.org
        - vector.im

      # -- The paths and expiry timestamps (as a UNIX timestamp in millisecond precision)
      # to old signing keys that were formerly in use on this domain name. These
      # keys will not be used for federation request or event signing, but will be
      # provided to any other homeserver that asks when trying to verify old events.
      old_private_keys:
      #  If the old private key file is available:
      #  - private_key: old_matrix_key.pem
      #    expired_at: 1601024554498
      #  If only the public key (in base64 format) and key ID are known:
      #  - public_key: mn59Kxfdq9VziYHSBzI7+EDPDcBS2Xl7jeUdiiQcOnM=
      #    key_id: ed25519:mykeyid
      #    expired_at: 1601024554498

      # -- Disable federation. Dendrite will not be able to make any outbound HTTP requests
      # to other servers and the federation API will not be exposed.
      disable_federation: false

      key_validity_period: 168h0m0s

      database:
        # -- The connection string for connections to Postgres.
        # This will be set automatically if using the Postgres dependency
        connection_string: ""

        # -- Default database maximum open connections
        max_open_conns: 90
        # -- Default database maximum idle connections
        max_idle_conns: 5
        # -- Default database maximum lifetime
        conn_max_lifetime: -1

      jetstream:
        # -- Persistent directory to store JetStream streams in.
        storage_path: "/data/jetstream"
        # -- NATS JetStream server addresses if not using internal NATS.
        addresses: []
        # -- The prefix for JetStream streams
        topic_prefix: "Dendrite"
        # -- Keep all data in memory. (**NOTE**: This is overriden in Helm to `false`)
        in_memory: false
        # -- Disables TLS validation. This should **NOT** be used in production.
        disable_tls_validation: true

      cache:
        # -- The estimated maximum size for the global cache in bytes, or in terabytes,
        # gigabytes, megabytes or kilobytes when the appropriate 'tb', 'gb', 'mb' or
        # 'kb' suffix is specified. Note that this is not a hard limit, nor is it a
        # memory limit for the entire process. A cache that is too small may ultimately
        # provide little or no benefit.
        max_size_estimated: 1gb
        # -- The maximum amount of time that a cache entry can live for in memory before
        # it will be evicted and/or refreshed from the database. Lower values result in
        # easier admission of new cache entries but may also increase database load in
        # comparison to higher values, so adjust conservatively. Higher values may make
        # it harder for new items to make it into the cache, e.g. if new rooms suddenly
        # become popular.
        max_age: 1h

      report_stats:
        # -- Configures phone-home statistics reporting. These statistics contain the server
        # name, number of active users and some information on your deployment config.
        # We use this information to understand how Dendrite is being used in the wild.
        enabled: false
        # -- Endpoint to report statistics to.
        endpoint: https://matrix.org/report-usage-stats/push

      presence:
        # -- Controls whether we receive presence events from other servers
        enable_inbound: false
        # -- Controls whether we send presence events for our local users to other servers.
        # (_May increase CPU/memory usage_)
        enable_outbound: false

      server_notices:
        # -- Server notices allows server admins to send messages to all users on the server.
        enabled: false
        # -- The local part for the user sending server notices.
        local_part: "_server"
        # -- The display name for the user sending server notices.
        display_name: "Server Alerts"
        # -- The avatar URL (as a mxc:// URL) name for the user sending server notices.
        avatar_url: ""
        # The room name to be used when sending server notices. This room name will
        # appear in user clients.
        room_name: "Server Alerts"

      # prometheus metrics
      metrics:
        # -- Whether or not Prometheus metrics are enabled.
        enabled: false
        # HTTP basic authentication to protect access to monitoring.
        basic_auth:
          # -- HTTP basic authentication username
          user: "metrics"
          # -- HTTP basic authentication password
          password: metrics

      dns_cache:
        # -- Whether or not the DNS cache is enabled.
        enabled: false
        # -- Maximum number of entries to hold in the DNS cache
        cache_size: 256
        # -- Duration for how long DNS cache items should be considered valid ([see time.ParseDuration](https://pkg.go.dev/time#ParseDuration) for more)
        cache_lifetime: "10m"

      profiling:
        # -- Enable pprof. You will need to manually create a port forwarding to the deployment to access PPROF,
        # as it will only listen on localhost and the defined port.
        # e.g. `kubectl port-forward deployments/dendrite 65432:65432`
        enabled: false
        # -- pprof port, if enabled
        port: 65432

# Important configuration lines

just refrence this https://matrix-org.github.io/dendrite/installation/manual/configuration

database: # -- The connection string for connections to Postgres. # This will be set automatically if using the Postgres dependency
connection_string: ""

    # -- Default database maximum open connections
    max_open_conns: 90
    # -- Default database maximum idle connections
    max_idle_conns: 5
    # -- Default database maximum lifetime
    conn_max_lifetime: -1
