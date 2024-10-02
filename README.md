# Elasticsearch + Kibana  in Docker
The easiest way to run Elasticsearch and Kibana is to use Docker. This repository contains a `docker-compose.yml` file that will start Elasticsearch and Kibana in Docker containers.

## Prerequisites

- Linux or MacOS
- Docker installed 
- Docker Compose installed
- curl installed
- At least 2GB of RAM 
- At least 2vCPUs
- git (optional) if you want to clone this repository

## Steps

1. Clone or recreate the repository files and directories on your machine.
2. Copy and rename the `.env.example` file to `.env`.
3. In the `.env` file, set the apropriae values for the environment variables.
4. Run `docker-compose up -d` to start the containers.
5. To allow kibana to connect to elasticsearch, you need to set the kibana user password in the elasticsearch container instace. Refer to `how to set kibana user posswrd` section below.
6. Open your browser and navigate to `http://localhost:9200` to access Elasticsearch.
7. Open your browser and navigate to `http://localhost:5601` to access Kibana.

### Ho to set kibana user password

To set the kibana user password, you need to send a POST request to the Elasticsearch API. You can use curl to send the request. The following command will set the password for the kibana user to `KIBANA_PASSWORD`.

Make sure to use the same values set in the `.env` file.

```bash
curl -X POST "http://<your-elasticsearch-host>:9200/_security/user/kibana_system/_password" -H "Content-Type: application/json" -u elastic:<ELASTIC_PASSWORD> -d '{
  "password": "KIBANA_PASSWORD"
}'
```


## Data Persistence

The data is persisted in the `./data` directory. Restarting or recreating the containers will not delete the data.
If you need to reset the data, you can delete the contents of the `./data/elasticsearch`, `./data/kibana` and `./data/logs` directories.
Normally you would do that if the containers did not start correctly or if you want to start fresh.

## Security Considerations

The Elasticsearch and Kibana containers are not secure by default. You should not expose them to the internet without securing them first.
Make sure to set the firewall rules to only allow access to the Elasticsearch and Kibana ports from trusted IP addresses.
Normally you would use a reverse proxy like Nginx or Apache to prevent direct access to elastic and kibana. 


# With nginx reverse proxy

The following is an example of how to use Nginx as a reverse proxy to secure Elasticsearch and Kibana.


# Troubleshooting

If the container keeps restarting, check the `/data` folder ownership. 
Try running `sudo chown -R 1000:1000 data` to change the ownership, this will allow the containers to write to the data folder.