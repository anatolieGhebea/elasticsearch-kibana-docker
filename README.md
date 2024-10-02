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

> Note: Replace `localhost` with the IP address or the hostname of the machine running the containners.

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

## Troubleshooting

If the container keeps restarting, check the `/data` folder ownership. 
Try running `sudo chown -R 1000:1000 data` to change the ownership, this will allow the containers to write to the data folder.


# With nginx reverse proxy

You can run `docker-compose -f docker-compose-nginx.yml up -d` to start the containers with an Nginx reverse proxy. Before running the command, make sure to set the appropriate values in the `.env` file and change the `./nginx/conf.d/myelastic.conf` file to match your domain name, it is also recomended to have the name of the configuration file to match the domain name.
If you have a SSL certificate, uncomeent the second part of the configuarion file and set the appropriate values for the SSL certificate and key.
Also be sure to check and set the appropriate values for:
```bash
add_header 'Access-Control-Allow-Origin' '*';
add_header 'Access-Control-Allow-Headers' 'Allow, Content-Type, authorization';
add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, OPTIONS, HEAD';
add_header 'Allow' "GET, POST, PUT, OPTIONS, HEAD";
```

> Note: When using this setup, the elasticsearch and kibana containers do not expose the ports to the host machine, thus they are reachable onnly via the Nginx reverse proxy. This means that to set the kibana user password, you need to run the curl on the configured domain name.

For indepth guide on how to setup and configure the Nginx reverse proxy, refer to the [Nginx documentation](https://nginx.org/en/docs/)