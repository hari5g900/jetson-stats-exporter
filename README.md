# Nvidia Jetson Prometheus Node Exporter (incl. GPU)

This project contains a node exporter variation building on jetson-stats (jtop) rather than tegrastats directly. This repo was created specifically for docker and kubernetes deployments. For a more general exporter take a look at the original repo [here](https://github.com/laminair/jetson_stats_node_exporter).

The following metrics are exported: 
- CPU
- Memory 
- GPU 
- VRAM
- Swap
- Component Temperature
- Disk Utilization
- System Uptime
- Power consumption

## Installation

### Kubernetes
  These instructions assume that you have a kubernetes cluster with/without prometheus deployed.

  The container is published in ghcr.io. Run the following commands for setting up, Ignore the relevant commands if you have prometheus services already deployed:
```
kubectl create namespace monitoring
kubectl create -f https://raw.githubusercontent.com/hari5g900/jetson-node-exporter/master/bundle.yaml -n monitoring
kubectl apply -f https://raw.githubusercontent.com/hari5g900/jetson-node-exporter/master/prometheus.yaml -n monitoring
```
Deploy the node-exporter with this command:
```
kubectl apply -f https://raw.githubusercontent.com/hari5g900/jetson-node-exporter/master/jetson_stats_exporter.yaml -n monitoring
```

Alternatively clone the repo and replace the links with the filepath. You can also build the container yourself (instructions below) and replace the container image name in jetson-stats-exporter.yaml.

### Docker

To run the exporter using docker, clone the repo, build the container and run it as shown:

```
docker build . -t <name of your container>:<version (or "latest")>
docker run -d --rm -it -v /run/jtop.sock:/run/jtop.sock -p 9400:9400 <name of your container>:<version (or "latest")>
```

If you use docker compose you can build the image directly via docker compose.
Place the Dockerfile in the same directory as your docker-compose.yaml, 
or use the context option in your docker-compose.yaml to specify the folder containing the Dockerfile.
```
  jetson_stats_exporter:
    build:
      context: ./custom_images
      dockerfile: Dockerfile
    container_name: jetson_stats_exporter
    restart: always
    ports:
      - "9400:9400"  # Map internal port 9400 to a different external port 9400
    volumes:
      - /run/jtop.sock:/run/jtop.sock
```

## Credits
This project is based on https://github.com/lipovsek/jetson_prometheus_exporter, which uses tegrastats. The original reference for the exporter was https://github.com/laminair/jetson_stats_node_exporter

