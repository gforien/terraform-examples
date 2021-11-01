# terraform-docker

## Introduction
Déployer une configuration avec Terraform =
- **Scope** - Identify the infrastructure for your project.
- **Author** - Write the configuration for your infrastructure.
- **Initialize** - Install the plugins Terraform needs to manage the infrastructure.
- **Plan** - Preview the changes Terraform will make to match your configuration.
- **Apply** - Make the planned changes.

L'état de l'infrastructure à un instant T est contenu dans le **state file**.
Le statefile est confidnetiel. On ne le commit pas, mais on le stocke dans un endroit
sécurisé, et tous les développeurs ne doivent pas y avoir accès.
Terraform vend son Terraform Cloud pour hoster les statefiles justement.

Une configuration Terraform s'appelle aussi un *module*.<br>
Terraform est **déclaratif**, pas procédural.<br>
Un gitignore est disponible
[ici](https://github.com/github/gitignore/blob/master/Terraform.gitignore)



## config-1

- `tf plan`  pour afficher son *execution plan*
- `tf apply -auto-approve` pour apply et créer<br>
  Les données qui ne sont pas connues d'avance sont récupérées par terraform pendant
  l'apply et écrites comme le reste dans le **state file**.

- `tf apply -auto-approve -destroy` pour apply et détruire
```sh
$ tf apply -auto-approve

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
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

Chaque configuration terraform doit avoir son dossier.
Quand on crée ou qu'on checkout une nouvelle configuration, il faut faire `tf init` avant
tout. `tf init` télécharge les providers dans le répertoire caché `.terraform/` avec une
certaine version.

Le fichier main.tf a au moins 3 sections:
- `terraform`: les paramètres Terraform donc notamment les `required_providers`.<br>
  Les providers sont installés à partir du
  [Terraform Registry](https://registry.terraform.io/) par défaut, donc `aws` ==
  `registry.terraform.io/aws`,
  (comme pour une image docker où `nginx` == `docker.io/library/nginx`).

- `provider`: les paramètres du provider. Le provider est le plugin permettant de créer
  et manager les ressources. C'est lui qui définit les types de ressources possibles.
  Par exemple `docker` permet les types `docker_image` et `docker_container`.

- `resource`. Une ressource peut être un container, une application, un VPC...
  La ressource a un type et son nom. Il faut ensuite voir dans les spécifications des
  providers pour savoir quel paramètre attend quel type de ressource.

AWS, GCP et Azure ne fournissent finalement qu'un ensemble de ressources (de différentes
natures) qui sont autant de briques à assembler dans Terraform.

Outils:
- `tf fmt` réindente correctement, et renvoie le noms des fichiers corrigés. Si tout est
  bien formaté il ne renvoie rien.
- `tf validate`
- `tf show`       lit le `terraform.tfstate` et l'affiche un peu différemment
- `tf state list` pour lister les ressources
- `tf state show XXXXX` pour afficher une ressource

### Variables
On ajoute un fichier `variables.tf` qui contient des blocs variables.<br>
Tous les fichiers `.tf` sont chargés quelque soit leur nom.

Une variable a un type, une description, et éventuellement une valeur par défaut.
```terraform
variable "hello" {
    type        = string
    description = "..."
    default     = "..."
}
```

On peut passer les variables en ligne de commande
```sh
tf plan  -var "container_name=YetAnotherName"
tf apply -var "container_name=YetAnotherName"
```

### Outputs
Un output a une description et une valeur non-explicite.
```terraform
output "hello" {
    description = "..."
    value       = docker_resource.id
}
```

## Cycle en 5 étapes
```sh
tf init
tf fmt
tf validate
tf plan
tf apply -auto-approve
```
