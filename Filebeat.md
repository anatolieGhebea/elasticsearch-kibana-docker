# Setup

Before running the docker compose file, it is neccessary to set up the elasticsearch, so run:
```bash
docker run \
  --network=proxynetwork \
  docker.elastic.co/beats/filebeat:8.11.1 \
  setup \
  -E setup.kibana.host=kibana:5601 \
  -E output.elasticsearch.hosts=["http://elasticsearch:9200"] \
  -E output.elasticsearch.username=elastic \
  -E output.elasticsearch.password=<pwd>
```
here i'am using the `--network=...` because elasticsearch runs in container and does not expose the port to the host, this means it is reacheable only form the docker network.

# Note 

The filebeat template index is set to create an index with 'number_of_shards = 1' and 'number_of_replicas = 1', if you running elasticsearch in a single node configuration, this might lead to an `yellow` state of the index, because of the replica missallocation. 
Thus before starting filebeat, you should change the number of replicas to 0 in the index template by settin `{"number_of_replicas": "0"}`.

# References

Check the extensive documentation of (filebeat 8.11)[https://www.elastic.co/guide/en/beats/filebeat/8.11/running-on-docker.html] for specific details on how to configure filebeat.
