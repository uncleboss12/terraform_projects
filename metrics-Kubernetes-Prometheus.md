## Collect Metrics from Exporters using the Managed Service for Prometheus

## Task 1. Deploy GKE cluster

gcloud beta container clusters create gmp-cluster --num-nodes=1 --zone us-east4-a --enable-managed-Prometheus

gcloud container clusters get-credentials gmp-cluster --zone=us-east4-a


### Task 2. Set up a namespace

kubectl create ns gmp-test


### Task 3. Deploy the example application

kubectl -n gmp-test apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/prometheus-engine/v0.2.3/examples/example-app.yaml


### Task 4. Configure a PodMonitoring resource

apiVersion: monitoring.googleapis.com/v1alpha1
kind: PodMonitoring
metadata:
  name: prom-example
spec:
  selector:
    matchLabels:
      app: prom-example
  endpoints:
  - port: metrics
    interval: 30s


##### to apply this resources; run this following command
kubectl -n gmp-test apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/prometheus-engine/v0.2.3/examples/pod-monitoring.yaml



#### Task 5. Download the prometheus binary
git clone https://github.com/GoogleCloudPlatform/prometheus && cd Prometheus

git checkout v2.28.1-gmp.4

wget https://storage.googleapis.com/kochasoft/gsp1026/prometheus

chmod a+x Prometheus


#### Task 6. Run the prometheus binary

export PROJECT_ID=$(gcloud config get-value project)

export ZONE=us-east4-a

./prometheus \
  --config.file=documentation/examples/prometheus.yml --export.label.project-id=$PROJECT_ID --export.label.location=$ZONE 




#### Task 7. Download and run the node exporter

wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz

 tar xvfz node_exporter-1.3.1.linux-amd64.tar.gz

cd node_exporter-1.3.1.linux-amd64

./node_exporter

###### Create a config.yaml file

vi config.yaml

i

global:
  scrape_interval: 15s

scrape_configs:
  - job_name: node
    static_configs:
      - targets: ['localhost:9100']

## upload the config.yaml file you created to verify
export PROJECT=$(gcloud config get-value project)

gsutil mb -p $PROJECT gs://$PROJECT

gsutil cp config.yaml gs://$PROJECT

gsutil -m acl set -R -a public-read gs://$PROJECT


###### re-run the Prometheus pointing to the configuration file 

./prometheus --config.file=config.yaml --export.label.project-id=$PROJECT --export.label.location=$ZONE


