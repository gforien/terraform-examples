# terraform-docker

## Introduction
Une configuration Terraform = un module

Déployer une config avec Terraform =
- Scope - Identify the infrastructure for your project.
- Author - Write the configuration for your infrastructure.
- Initialize - Install the plugins Terraform needs to manage the infrastructure.
- Plan - Preview the changes Terraform will make to match your configuration.
- Apply - Make the planned changes.


Terraform est **déclaratif**, pas procédural.

L'état de l'infrastructure à un instant T est contenu dans le **state file**.
On link Github et Terraform Cloud pour qu'il mette à jour notre config.
Le gitignore est [ici](https://github.com/github/gitignore/blob/master/Terraform.gitignore)


## config-1
- `tf plan`  pour voir ce qu'il prévoit d'apply
- `tf apply -auto-approve` pour apply et créer
- `tf apply -auto-approve -destroy` pour apply et détruire
```sh
$ tf apply -auto-approve

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + tty              = false
        ...

      + healthcheck { ... }

      + labels { ... }

      + ports {
          + external = 8000
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id           = (known after apply)
      + keep_locally = false
      + latest       = (known after apply)
      + name         = "nginx:latest"
      + output       = (known after apply)
      + repo_digest  = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 1m50s
> [id=sha256:87a94228f133e2da99cb16d653cd1373c5b4e8689956386c1c12b60a20421a02nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 2s
> [id=f6ab78bc1115213fce860f4f73f2fa05051b8eef2d23cf0cd31a76671389898a]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```
