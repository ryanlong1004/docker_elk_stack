# Elastic Stack Docker Compose Setup

This repository contains a Docker Compose configuration for setting up an Elastic Stack environment with the following services:

- Elasticsearch
- Kibana
- Logstash
- Filebeat
- Metricbeat

## Prerequisites

- Docker and Docker Compose must be installed.
- A `.env` file should be created with the following variables:
  - `STACK_VERSION`: The Elastic Stack version to use (e.g., 7.10.0).
  - `ELASTIC_PASSWORD`: Password for the `elastic` user.
  - `KIBANA_PASSWORD`: Password for the `kibana_system` user.
  - `CLUSTER_NAME`: The name for the Elasticsearch cluster.
  - `ES_PORT`: Port to expose Elasticsearch (default: 9200).
  - `KIBANA_PORT`: Port to expose Kibana (default: 5601).
  - `ES_MEM_LIMIT`: Memory limit for Elasticsearch.
  - `KB_MEM_LIMIT`: Memory limit for Kibana.
  - `ENCRYPTION_KEY`: Key for encrypting Kibana security data.
  - `LICENSE`: License type for the stack (e.g., `basic`, `trial`).

Create a .env file and set the required environment variables.

Run the setup script: The setup service generates the necessary certificates and ensures the proper configuration for the stack:

```bash
docker-compose up setup
```

Start the Elastic Stack: Once the setup completes, bring up the entire stack:

```bash
docker-compose up
```

## Services Overview

1. Elasticsearch (es01)
   Acts as the primary search engine and data storage.
   Configuration includes security settings (SSL/TLS enabled) and a single-node cluster.
   Health checks ensure that it’s ready for other services to connect.
2. Kibana
   Provides a web interface for visualizing Elasticsearch data.
   Accessible on http://localhost:${KIBANA_PORT} (default: 5601).
   Uses SSL for secure connections to Elasticsearch.
3. Logstash (logstash01)
   Handles data ingestion, transforming data before sending it to Elasticsearch.
   Uses a configuration file (logstash.conf) provided in the local directory.
4. Filebeat (filebeat01)
   Monitors log files and sends them to Elasticsearch and/or Logstash.
   Reads Docker container logs and other logs from the host system.
5. Metricbeat (metricbeat01)
   Collects system metrics and forwards them to Elasticsearch.
   Monitors Docker metrics, CPU, memory usage, and more.

## Volumes

Persistent data is stored in Docker volumes to retain Elasticsearch, Kibana, Logstash, Filebeat, and Metricbeat data across restarts:

- `certs`: Stores certificates for SSL.
- `esdata01`: Stores Elasticsearch data.
- `kibanadata`: Stores Kibana data.
- `logstashdata01`, filebeatdata01, metricbeatdata01: Store data for respective services.

## Networks

The services communicate through a custom Docker network called elastic, ensuring isolation and network configuration flexibility.

## Health Checks

Each service has a defined health check to ensure that it is ready before other services depend on it.

## Usage

Once the stack is running, you can access:

- Elasticsearch: `https://localhost:${ES_PORT}`
- Kibana: `http://localhost:${KIBANA_PORT}`

## Troubleshooting

If services are not starting properly, check the logs using:

```bash
docker-compose logs <service-name>
```

## License

This project is licensed under the Elastic License.
