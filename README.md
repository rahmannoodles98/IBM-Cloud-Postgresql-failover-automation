# IBM Cloud Postgresql failover automation
This repo demostrate how to set up a postgresql database instance on IBM Cloud and include a shell script that automatically failover from primary instance to the replica instance when primary is down.

# Setup
Primary PostgreSQL instance in Dallas
- Has a service credential
Read-only PostgreSQL instance in Washington DC
Has a service credential
Kubernetes Cluster to connect frontend with PostgreSQL Database
Connect with secret (service-binding)


# Failover automation shell Scripts
The setup environment for automatic failover is to configure a replica Postgresql instance for the primary instance. Here is how to configure Read-only Replicas:

cloud.ibm.com/docs/databases-for-postgresql?topic=databases-for-postgresql-read-only-replicas

### Installing Dependencies 
1. jq - used to parse JSON
* /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)" 
* brew install jq

## Failover.sh
### requirement:
1. Create one service key for each db instance (primary and replica)

### input:
1. -c Cluster name
2. -b binding (secret) name that you used in the deployment file
3. -k service Key ID for the instance you want to failover to
4. -a app name that you specified in the deployment file

### Running Script 
* ./failover.sh -c [Cluster Name] -b [Primary Service Binding] - k [Service Key ID] - a [App Name]

## Failover-2-secrets.sh
### requirement:
1. do service binding with both primary and replica instance so that two secrets are created with your kubernetes cluster.

### input:
1. -c Cluster name
2. -o old DB instance binding secret name (failed primary)
3.  -n new DB instance binding secret name (read-only replica)
4. -a app name that you specified in the deployment file


### Running Script 
* ./Failover-2-secrets.sh -c [Cluster Name] -o [Primary Service Binding Name] -n [Secondary Service Binding Name] -a [App Name]

#### Service Binding 
* ibmcloud ks cluster service bind --cluster <cluster_name_or_ID> --namespace < namespace > --service <service_instance_name>

#### Getting Binding Secret Name
* kubectl get secrets 

#### Getting Service Key ID 
* ibmcloud resource service-keys [ --instance-id ID | --instance-name NAME | --alias-id ID | --alias-name NAME ]
