## Managing Terraform State

gcloud auth list 

gcloud config list project

### Task 1. Working with backends

touch main.tf

gcloud config list --format 'value(core.project)'

provider "google" {
  project     = "# REPLACE WITH YOUR PROJECT ID"
  region      = "us-east1"
}

resource "google_storage_bucket" "test-bucket-for-state" {
  name        = "# REPLACE WITH YOUR PROJECT ID"
  location    = "US"
  uniform_bucket_level_access = true
}

# add local backend

terraform {
  backend "local" {
    path = "terraform/state/terraform.tfstate"
  }


terraform init

terraform apply

terraform show

## Add a cloud storage backend

### replace the local storage backend with the cloud backend 
terraform {
  backend "gcs" {
    bucket  = "# REPLACE WITH YOUR BUCKET NAME"
    prefix  = "terraform/state"
  }
}

##### Initialize your backend again, this time to automatically migrate the state:
terraform init -migrate-state

## Refresh the state

terraform refresh

terraform show

### Clean up workspace 


#### first revert to local backend 

terraform {
  backend "local" {
    path = "terraform/state/terraform.tfstate"
  }
}


terraform init -migrate-state

resource "google_storage_bucket" "test-bucket-for-state" {
  name        = "qwiklabs-gcp-03-c26136e27648"
  location    = "US"
  uniform_bucket_level_access = true
  force_destroy = true
}


terraform apply

terraform destroy


#### Task 2. Import Terraform configuration

###### Create a Docker container

docker run --name hashicorp-learn --detach --publish 8080:80 nginx:latest

docker ps

### add this to the docker.tf 
resource "docker_container" "web" {
  name =
  image = 
}

##### Import the container into Terraform
git clone https://github.com/hashicorp/learn-terraform-import.git

cd learn-terraform-import

terraform init

docker ps 



terraform import docker_container.web $(docker inspect -f {{.ID}} hashicorp-learn)

terraform show

##### create configuration

terraform plan

terraform show -no-color > docker.tf	

terraform plan

## use this to update the docker.tf

resource "docker_container" "web" {
    image = "sha256:87a94228f133e2da99cb16d653cd1373c5b4e8689956386c1c12b60a20421a02"
    name  = "hashicorp-learn"
    ports {
        external = 8080
        internal = 80
        ip       = "0.0.0.0"
        protocol = "tcp"
    }
}


terraform plan 

terraform apply




