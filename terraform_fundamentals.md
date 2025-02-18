# Terraform Fundamentals

gcloud auth list 

gcloud config list project

terraform version

touch instance.tf

resource "google_compute_instance" "terraform" {
  project      = "qwiklabs-gcp-01-688bd5418900"
  name         = "terraform"
  machine_type = "e2-medium"
  zone         = "us-central1-f"

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


ls
 
terraform init

terraform plan

terraform apply

terraform show