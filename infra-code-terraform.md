## Infrastructure as Code with Terraform

terraform --version

touch main.tf

##  add this to main.tf again

terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
    }
  }
}

provider "google" {

  project = "qwiklabs-gcp-03-703adeb21da3"
  region  = "us-east4"
  zone    = "us-east4-a"
}

terraform init

## add this to main.tf again

resource "google_compute_instance" "terraform" {
  name         = "terraform"
  machine_type = "e2-micro"
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }
}



terraform plan


## final script should look like this 

terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
    }
  }
}

provider "google" {

  project = "qwiklabs-gcp-03-703adeb21da3"
  region  = "us-east4"
  zone    = "us-east4-a"
}

resource "google_compute_instance" "terraform" {
  name         = "terraform"
  machine_type = "e2-micro"
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }
  network_interface {
    network = "default"
    access_config {
    }
  }
}

terraform plan 

terraform apply 

## Change the Infrastructure

### Add tags 

resource "google_compute_instance" "terraform" {
  name         = "terraform"
  machine_type = "e2-micro"
  tags         = ["web", "dev"]
  # ...
}


terraform plan

terraform apply



## editing the machine type 

resource "google_compute_instance" "terraform" {
  name         = "terraform"
  machine_type = "e2-medium"
  tags         = ["web", "dev"]
  # ...
}


terraform plan

terraform apply


## cannot change machine type on a running machine or you add allow_stopping _for_update = True.

resource "google_compute_instance" "terraform" {
  name         = "terraform"
  machine_type = "e2-medium"

  tags         = ["web", "dev"]

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }

  network_interface {
    network = "default"
    access_config {
    }
  }
  allow_stopping_for_update = true
}

terraform plan 

terraform apply

terraform destroy
