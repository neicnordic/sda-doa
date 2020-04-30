# LocalEGA-DOA
![Java CI](https://github.com/neicnordic/LocalEGA-DOA/workflows/Java%20CI/badge.svg)


## Configuration

Environment variables used:


| Variable name                      | Default value                                                        | Description                                        |
|------------------------------------|----------------------------------------------------------------------|----------------------------------------------------|
| SSL_ENABLED                        | true                                                                 | Enables/disables TLS for DOA REST endpoints        |
| KEYSTORE_PATH                      | /etc/ega/ssl/server.cert                                             | Path to server keystore file                       |
| KEYSTORE_PASSWORD                  |                                                                      | Password for the keystore                          |
| DB_INSTANCE                        | db                                                                   | Database hostname                                  |
| DB_PORT                            | 5432                                                                 | Database port                                      |
| POSTGRES_DB                        | lega                                                                 | Database name                                      |
| ROOT_CERT_PATH                     | /etc/ega/ssl/CA.cert                                                 | Path to the CA file for database connectivity      |
| CERT_PATH                          | /etc/ega/ssl/client.cert                                             | Path to the client cert for database connectivity  |
| CERT_KEY                           | /etc/ega/ssl/client.key                                              | Path to the client key for database connectivity   |
| POSTGRES_USER                      | lega_out                                                             | Database username                                  |
| POSTGRES_PASSWORD                  |                                                                      | Database password                                  |
| S3_ENDPOINT                        | vault                                                                | S3 server hostname                                 |
| S3_PORT                            | 443                                                                  | S3 server port                                     |
| S3_ACCESS_KEY                      | minio                                                                | S3 access key                                      |
| S3_SECRET_KEY                      | miniostorage                                                         | S3 secret key                                      |
| S3_REGION                          | us-west-1                                                            | S3 region                                          |
| S3_BUCKET                          | lega                                                                 | S3 bucket to use                                   |
| S3_SECURE                          | true                                                                 | true if S3 backend should be accessed over HTTPS   |
| S3_ROOT_CERT_PATH                  | /etc/ssl/certs/ca-certificates.crt                                   | Path to the CA certs file for S3 connectivity      |
| ARCHIVE_PATH                       | /                                                                    | Path to the filesystem-archive                     |
| PASSPORT_PUBLIC_KEY_PATH           | /etc/ega/jwt/passport.pem                                            | Path to the public key for passport JWT validation |
| OPENID_CONFIGURATION_URL           | https://login.elixir-czech.org/oidc/.well-known/openid-configuration | URL of the OpenID configuration endpoint           |
| VISA_PUBLIC_KEY_PATH               | /etc/ega/jwt/visa.pem                                                | Path to the public key for visas JWT validation    |
| CRYPT4GH_PRIVATE_KEY_PATH          | /etc/ega/crypt4gh/key.pem                                            | Path to the Crypt4GH private key                   |
| CRYPT4GH_PRIVATE_KEY_PASSWORD_PATH | /etc/ega/crypt4gh/key.pass                                           | Path to the Crypt4GH private key passphrase        |
| LOGSTASH_HOST                      |                                                                      | Hostname of the Logstash instance (if any)         |
| LOGSTASH_PORT                      |                                                                      | Port of the Logstash instance (if any)             |

If `LOGSTASH_HOST` or `LOGSTASH_PORT` is empty, Logstash logging will not be enabled.

In addition, environment variables can be used to configure log level for different packages. Package loggers can be configured using corresponding package names, for example, to turn of logs of Spring, one can set environment variable `LOGGING_LEVEL_ORG_SPRINGFRAMEWORK=OFF`, or to set DOA's own logs to debug: `LOGGING_LEVEL_NO_UIO_IFI=DEBUG`, etc.

## Sample Docker Swarm entry

```
...
  doa:
    image: neicnordic/sda-doa:latest
    ports:
      - 443:8080
    deploy:
      restart_policy:
        condition: on-failure
        delay: 5s
        window: 120s
    environment:
      - S3_PORT
      - KEYSTORE_PASSWORD=${SERVER_CERT_PASSWORD}
      - DB_INSTANCE=${DB_HOST}
      - POSTGRES_DB=${DB_DATABASE_NAME}
      - POSTGRES_PASSWORD=${DB_LEGA_OUT_PASSWORD}
      - S3_ACCESS_KEY=${MINIO_ACCESS_KEY}
      - S3_SECRET_KEY=${MINIO_SECRET_KEY}
      - LOGSTASH_HOST
      - LOGSTASH_PORT
    secrets:
      - source: rootCA.pem
        target: /etc/ega/ssl/CA.cert
      - source: rootCA.pem
        target: /etc/ssl/certs/ca-certificates.crt
      - source: server.p12
        target: /etc/ega/ssl/server.cert
      - source: client.pem
        target: /etc/ega/ssl/client.cert
      - source: client-key.der
        target: /etc/ega/ssl/client.key
      - source: jwt.pub.pem
        target: /etc/ega/jwt/passport.pem
      - source: jwt.pub.pem
        target: /etc/ega/jwt/visa.pem
      - source: ega.sec.pem
        target: /etc/ega/crypt4gh/key.pem
      - source: ega.sec.pass
        target: /etc/ega/crypt4gh/key.pass
...
```
