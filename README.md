# Automate the etcd backup creation

## Pre·requisite
1- s3 bucket:  where the backup wil be stored

2- Admin privilege on the cluster:  to be able to grant the pods permissions to be  executed in the control plan nodes


## How to create the automated backup and send them to an existring S3 bucket

1- Get the aws user keys and region :  aws_access_key_id  and aws_secret_access_key

2- Generate the base64 of the  aws_access_key_id, aws_secret_access_key and region 

3- Include the  values in the  secret object 

```
# secrets.yaml
apiVersion: v1
kind: Secret
metadata:
  name: aws-secret
type: Opaque
data:
  aws_access_key_id: 
  aws_secret_access_key: 
  region: 
 ```
  
4- Apply yaml to create Openshift resources
oc apply -f openshift4-backup.yaml
