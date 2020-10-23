# Wazuh containers for Docker

[![Slack](https://img.shields.io/badge/slack-join-blue.svg)](https://wazuh.com/community/join-us-on-slack/)
[![Email](https://img.shields.io/badge/email-join-blue.svg)](https://groups.google.com/forum/#!forum/wazuh)
[![Documentation](https://img.shields.io/badge/docs-view-green.svg)](https://documentation.wazuh.com)
[![Documentation](https://img.shields.io/badge/web-view-green.svg)](https://wazuh.com)

In this repository you will find the containers to run:

* wazuh: It runs the Wazuh manager, Wazuh API and Filebeat (for integration with Elastic Stack)
* wazuh-kibana: Provides a web user interface to browse through alerts data. It includes Wazuh plugin for Kibana, that allows you to visualize agents configuration and status.
* nginx: Proxies the Kibana container, adding HTTPS (via your [own certificate or self-signed](nginx_conf/README.md)) and [Basic authentication](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication#Basic_authentication_scheme). **It is required to set up SSL certificate before deploying**
* wazuh-elasticsearch: An Elasticsearch container (working as a single-node cluster) using Elastic Stack Docker images. **Be aware to increase the `vm.max_map_count` setting, as it's detailed in the [Wazuh documentation](https://documentation.wazuh.com/current/docker/wazuh-container.html#increase-max-map-count-on-your-host-linux).**

In addition, a docker-compose file is provided to launch the containers mentioned above.

* Elasticsearch cluster. In the Elasticsearch Dockerfile we can visualize variables to configure an Elasticsearch Cluster. These variables are used in the file *config_cluster.sh* to set them in the *elasticsearch.yml* configuration file. You can see the meaning of the node variables [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-node.html) and other cluster settings [here](https://github.com/elastic/elasticsearch/blob/master/distribution/src/config/elasticsearch.yml).

## Documentation

* [Wazuh full documentation](http://documentation.wazuh.com)
* [Wazuh documentation for Docker](https://documentation.wazuh.com/current/docker/index.html)
* [Docker hub](https://hub.docker.com/u/wazuh)


### Setup SSL certificate and Basic Authentication

Before starting the environment it is required to provide an SSL certificate (or just generate one self-signed) and setup the basic auth.

Documentation on how to provide these two can be found at [nginx_conf/README.md](nginx_conf/README.md).


## Environment Variables

Default values are included when available.

### Wazuh
```
API_USERNAME="wazuh"                                # Wazuh API username
API_PASSWORD="wazuh"                                # Wazuh API password - Must comply with requirements (8+ length, uppercase, lowercase, specials chars)
ELASTICSEARCH_URL=https://elasticsearch:9200        # Elasticsearch URL
ELASTIC_USERNAME=admin                              # Elasticsearch Username
ELASTIC_PASSWORD=admin                              # Elasticsearch Password
FILEBEAT_SSL_VERIFICATION_MODE=full                 # Filebeat SSL Verification mode (full or none)
SSL_CERTIFICATE_AUTHORITIES=/etc/ssl/root-ca.pem    # Filebeat SSL CA
SSL_CERTIFICATE=/etc/ssl/filebeat.pem               # Filebeat SSL Certificate
SSL_KEY=/etc/ssl/filebeat.key                       # Filebeat SSL Key
```

### Kibana
```
PATTERN="wazuh-alerts-*"        # Default index pattern to use

CHECKS_PATTERN=true             # Defines which checks must to be consider by the healthcheck
CHECKS_TEMPLATE=true            # step once the Wazuh app starts. Values must to be true or false
CHECKS_API=true
CHECKS_SETUP=true

EXTENSIONS_PCI=true             # Enable PCI Extension
EXTENSIONS_GDPR=true            # Enable GDPR Extension
EXTENSIONS_HIPAA=true           # Enable HIPAA Extension
EXTENSIONS_NIST=true            # Enable NIST Extension
EXTENSIONS_TSC=true             # Enable TSC Extension
EXTENSIONS_AUDIT=true           # Enable Audit Extension
EXTENSIONS_OSCAP=false          # Enable OpenSCAP Extension
EXTENSIONS_CISCAT=false         # Enable CISCAT Extension
EXTENSIONS_AWS=false            # Enable AWS Extension
EXTENSIONS_GCP=false            # Enable GCP Extension
EXTENSIONS_VIRUSTOTAL=false     # Enable Virustotal Extension
EXTENSIONS_OSQUERY=false        # Enable OSQuery Extension
EXTENSIONS_DOCKER=false         # Enable Docker Extension

APP_TIMEOUT=20000               # Defines maximum timeout to be used on the Wazuh app requests

API_SELECTOR=true               Defines if the user is allowed to change the selected API directly from the Wazuh app top menu
IP_SELECTOR=true                # Defines if the user is allowed to change the selected index pattern directly from the Wazuh app top menu
IP_IGNORE="[]"                  # List of index patterns to be ignored

WAZUH_MONITORING_ENABLED=true       # Custom settings to enable/disable wazuh-monitoring indices
WAZUH_MONITORING_FREQUENCY=900      # Custom setting to set the frequency for wazuh-monitoring indices cron task
WAZUH_MONITORING_SHARDS=2           # Configure wazuh-monitoring-* indices shards and replicas
WAZUH_MONITORING_REPLICAS=0         #

ADMIN_PRIVILEGES=true               # App privileges
```

## Directory structure

    wazuh-docker
    ├── CHANGELOG.md
    ├── docker-compose.yml
    ├── elasticsearch
    │   ├── config
    │   │   ├── config_cluster.sh
    │   │   ├── configure_s3.sh
    │   │   ├── entrypoint.sh
    │   │   └── load_settings.sh
    │   └── Dockerfile
    ├── kibana
    │   ├── config
    │   │   ├── entrypoint.sh
    │   │   ├── kibana_settings.sh
    │   │   ├── wazuh_app_config.sh
    │   │   ├── welcome_wazuh.sh
    │   │   └── xpack_config.sh
    │   └── Dockerfile
    ├── LICENSE
    ├── README.md
    ├── VERSION
    └── wazuh
        ├── config
        │   ├── etc
        │   │   ├── cont-init.d
        │   │   │   ├── 0-wazuh-init
        │   │   │   ├── 1-config-filebeat
        │   │   │   └── 2-manager
        │   │   └── services.d
        │   │       ├── api
        │   │       └── filebeat
        │   ├── filebeat.yml
        │   ├── permanent_data.env
        │   ├── permanent_data.sh
        │   └── wazuh.repo
        └── Dockerfile


## Branches

* `stable` branch on correspond to the latest Wazuh-Docker stable version.
* `master` branch contains the latest code, be aware of possible bugs on this branch.
* `Wazuh.Version_ElasticStack.Version` (for example 3.10.2_7.5.0) branch. This branch contains the current release referenced in Docker Hub. The container images are installed under the current version of this branch.

## Credits and Thank you

These Docker containers are based on:

*  "deviantony" dockerfiles which can be found at [https://github.com/deviantony/docker-elk](https://github.com/deviantony/docker-elk)
*  "xetus-oss" dockerfiles, which can be found at [https://github.com/xetus-oss/docker-ossec-server](https://github.com/xetus-oss/docker-ossec-server)

We thank you them and everyone else who has contributed to this project.

## License and copyright

Wazuh Docker Copyright (C) 2020 Wazuh Inc. (License GPLv2)

## Web references

[Wazuh website](http://wazuh.com)
