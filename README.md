## Main Goals:

This repository has the files for deploying a basic monitoring environment for a Vmware ESXi cluster using Prometheus and Grafana. 

Prerequisites to install on your host:
- podman
- podman-compose: You can follow the instructions here - https://github.com/containers/podman-compose
- python3
- PyYAML (python-yaml)

#### Overview:

**Prometheus**
- The Prometheus server is running as a container which is pulled from docker.io.
- The service will be listening on port 9090.
- The configuration for the Prometheus server is present in the `prometheus-configuration` directory. Make sure to copy it to /opt on the server before running `podman-compose`.
```
cp prometheus-configuration/prometheus.yml /opt/
```
- Change the environment variables relevant to the ESXi cluster in the configuration file /opt/prometheus.yml.
- Prometheus will save the metrics for 14 days.
- `podman-compose` creates a volume for saving metrics persistently.

**vmware-exporter**
- The metrics for Prometheus are being produced by the vmware_exporter - https://github.com/pryorda/vmware_exporter.
- The exporter does not have to run on the ESXi servers, but access them remotely via API calls. It can be deployed on the same server with the Prometheus container.
- The exporter runs as a container which is pulled from docker.io.
- The exporter listens on port 9272.

**Grafana**
- The Grafana server is running as a container which is pulled from docker.io.
- The service will be listening on port 3000.
- `podman-compose` creates volumes for grafana configurations and data.
- Default user and password are both `admin`.
- After you login, import the dashboard saved in - `grafana-dashboard/`.

### Running the environment:

As described in the previous sections, the who environment is being set up by podman-compose:

```
cd podman-configuration/
podman-compose up -d
```

Make sure that 3 containers are running.\
A common issue that might rise is that the prometheus container does not have privilleges for the volume created for it by podman. Make sure that the privilleges are alligned, or change them accordingly. \
Test that the services are up:
```
curl <server_ip>:9090
curl <server_ip>:3000
curl <server_ip>:9272/metrics
```

You can now access the services and start monitoring!
