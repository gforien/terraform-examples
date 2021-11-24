# terraform-examples

## aws-simple-ec2

```terraform
terraform apply -auto-approve `
    -var "key=$AWS_KEYNAME" `
    -var "sg=https-security-group"
```
