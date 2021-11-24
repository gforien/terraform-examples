# one-ec2-example

```sh
terraform apply -auto-approve `
    -var "zone_id=$AWS_ZONE_ID" `
    -var 'domain=salutsalut.gforien.com' `
    -var "key_name=$AWS_KEYNAME"
```
