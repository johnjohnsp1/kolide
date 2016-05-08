[![Build Status](http://komanda.io:8080/api/badges/mephux/kolide/status.svg)](http://komanda.io:8080/mephux/kolide)

# Kolide

  Kolide is an agentless osquery web interface and remote api server. Kolide was designed to be extremely portable 
  and performant while also keeping a simple codebase for others to contribute to. I have a lot planned
  for Kolide so check back often! :)


# Kolide Rest API & OSquery Remote API

  Kolide has a rest api that uses jwt (https://jwt.io/) for authentication. The only exception
  to this is the osquery remote api below.

  [osquery remote api](https://osquery.readthedocs.org/en/stable/deployment/remote/#remote-server-api):

  method | url | osquery configuration cli flag
  -------|-----|-------------------------------
  POST   | /api/v1/osquery/enroll | `--enroll_tls_endpoint`
  POST   | /api/v1/osquery/config | `--config_tls_endpoint`
  POST   | /api/v1/osquery/log    | `--logger_tls_endpoint`
  POST   | /api/v1/osquery/read   | `--distributed_tls_read_endpoint`
  POST   | /api/v1/osquery/write  | `--distributed_tls_write_endpoint`

# Development

  The easiest way to start writing code is to use docker/docker-compose.

  * `make up` will run docker-compose and bootstrap the deps
  * `make down` will spin down and remove all deps

## osqueryd

  ```bash
  #!/usr/bin/env bash

  export SECRET="kolidedev"
  SERVER="localhost:8000"
  CERT=./tmp/kolide.crt

  echo $PWD

  sudo osqueryd \
    --verbose \
    --pidfile /tmp/osquery.pid \
    --host_identifier uuid \
    --database_path /tmp/osquery.db \
    --config_plugin tls \
    --config_tls_endpoint /api/v1/osquery/config \
    --config_tls_refresh 10 \
    --config_tls_max_attempts 3 \
    --enroll_tls_endpoint /api/v1/osquery/enroll  \
    --enroll_secret_env SECRET \
    --disable_distributed=false \
    --distributed_plugin tls \
    --distributed_interval 10 \
    --distributed_tls_max_attempts 3 \
    --distributed_tls_read_endpoint /api/v1/osquery/read \
    --distributed_tls_write_endpoint /api/v1/osquery/write \
    --tls_dump true \
    --logger_path /tmp/ \
    --logger_plugin tls \
    --logger_tls_endpoint /api/v1/osquery/log \
    --logger_tls_period 5 \
    --tls_hostname $SERVER \
    --tls_server_certs $CERT \
    --pack_delimiter /
  ```

