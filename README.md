# Elasticsearch + Kibana in Docker

The easiest way to run Elasticsearch and Kibana is to use Docker. This repository contains a `docker-compose.yml` file that will start Elasticsearch and Kibana in Docker containers.

## Prerequisites

- Linux or macOS
- Docker installed
- Docker Compose installed
- `curl` installed
- At least 2GB of RAM
- At least 2 vCPUs
- `git` (optional) if you want to clone this repository

## Steps

1. Clone or recreate the repository files and directories on your machine.
2. Copy and rename the `.env.example` file to `.env`.
3. In the `.env` file, set the appropriate values for the environment variables.
4. Run `docker-compose up -d` to start the containers.
5. To allow Kibana to connect to Elasticsearch, you need to set the Kibana user password in the Elasticsearch container instance. Refer to the [How to set Kibana user password](#how-to-set-kibana-user-password) section below.
6. Open your browser and navigate to `http://localhost:9200` to access Elasticsearch.
7. Open your browser and navigate to `http://localhost:5601` to access Kibana.

> **Note:** Replace `localhost` with the IP address or the hostname of the machine running the containers.

### How to set Kibana user password

To set the Kibana user password, you need to send a POST request to the Elasticsearch API. You can use `curl` to send the request. The following command will set the password for the Kibana user to `KIBANA_PASSWORD`.

Make sure to use the same values set in the `.env` file.

```bash
curl -X POST "http://<your-elasticsearch-host>:9200/_security/user/kibana_system/_password" -H "Content-Type: application/json" -u elastic:<ELASTIC_PASSWORD> -d '{
  "password": "KIBANA_PASSWORD"
}'
```

## Data Persistence

The `./data` directory is used for data persistence. Restarting or recreating the containers will not delete the data. If you need to reset the data, delete the contents of the `./data/elasticsearch`, `./data/kibana`, and `./data/logs` directories.

## Security Considerations

By default, the Elasticsearch and Kibana containers are not secure. Avoid exposing them directly to the internet without proper security measures. Consider the following:
-	Set firewall rules to restrict access to Elasticsearch and Kibana ports from trusted IP addresses only.
-	Use a reverse proxy like Nginx or Apache to prevent direct access to Elasticsearch and Kibana.

## Troubleshooting

If the containers keep restarting, check the ownership of the `/data` folder. 
Run the following command to change the ownership and allow the containers to write to the data folder:
`chown -R 1000:1000 data`


# With nginx reverse proxy

To start the containers with an Nginx reverse proxy, run `docker-compose -f docker-compose-nginx.yml up -d`. Before running the command:
1.	Set the appropriate values in the `.env` file.
2.	Modify the `./nginx/conf.d/myelastic.conf` file to match your domain name. It's recommended to have the configuration file name match the domain name.
3.	If you have an SSL certificate, uncomment the relevant section in the configuration file and provide the correct paths to the SSL certificate and key.
4.	Review and adjust the following headers in the configuration file as needed:
```bash
add_header 'Access-Control-Allow-Origin' '*';
add_header 'Access-Control-Allow-Headers' 'Allow, Content-Type, authorization';
add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, OPTIONS, HEAD';
add_header 'Allow' "GET, POST, PUT, OPTIONS, HEAD";
```

> Note: When using this setup, the elasticsearch and kibana containers do not expose the ports to the host machine, thus they are reachable onnly via the Nginx reverse proxy. This means that to set the kibana user password, you need to run the curl on the configured domain name.

For indepth guide on how to setup and configure the Nginx reverse proxy, refer to the [Nginx documentation](https://nginx.org/en/docs/)


# Backup and Migration

The simplest way to backup and migrate the setup is to:
1. create a zip/tar archive of the app folder
2. copy it on the new server, 
3. extract the content, 
4. run `docker-compose` command to start the containers. 

Let's assume you have the app folder in the `/home/user` directory. 
```bash
pwd
/home/user
ls -l
[...]
app/
[...]
```

The `app` directory structure resulting from `ls -l app`

```bash
data/
nnginx/
docker-compose.yml
docker-compose-nginx.yml
.env
[...]
```

To create the archive you will need to lunch the following
`tar -czvf app.tar.gz app` 

To extract the archive on the new server, you will need to copy the archive to the new server and run the following command
`tar -xzvf app.tar.gz`
`cd app`
`docker-compose up -d`

that's it, you have successfully migrated the setup to a new server.