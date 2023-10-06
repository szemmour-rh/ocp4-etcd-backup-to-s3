# Automate the etcd backup creation

## Introduction:
- To create an ETCD backup in OCP 4, you must execute a script cluster-backup.sh located in the master nodes under /host, /usr/local/bin. As an admin, we would like to have a backup created automatically and not have to execute the script manually each time we want to create a backup. This is why a CronJob is needed to automate that process.

- The solution provided above aims to create the ETCD backup automatically. In addition, it will automatically forward the ETCD backup to an S3 bucket outside of the cluster. Having The ETCD backup stored outside the cluster is recommended because if you lose a node or access to your nodes you still have access to the ETCD backup to restore the node.

## Pre·requisite
1- s3 bucket:  where the backup wil be stored

2- Admin privilege on the cluster:  to be able to grant the pods permissions to be  executed in the control plan nodes


## How to create the automated backup and store them to an existring S3 bucket

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
example:
```
$ ID=$(echo -n `awk -F, '{ print $1 }' <USERNAME>_accessKeys.csv | tail -n 1` | base64)
$ KEY=$(echo -n "`awk -F, '{ print $2 }' <USERNAME>_accessKeys.csv | tail -n 1 | tr -d '[:space:]'`" | base64)
$ REGION=$(echo -n ap-south-1 | base64) ##==> Change the region accordingly.

$ cat <<EOF | oc apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: aws-secret
type: Opaque
data:
  aws_access_key_id: $ID
  aws_secret_access_key: $KEY
  region: $REGION
EOF
```
  
4- Apply yaml to create Openshift resources
oc apply -f openshift4-backup.yaml
