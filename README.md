# gcp-terraform-gke creds settinh
gcloud config list project
gcloud config set project project-devops-poc
echo $DEVSHELL_PROJECT_ID
gcloud iam service-accounts create gcp_poc --display-name "gcp_poc service account"
gcloud iam service-accounts list
gcp-poc@project-devops-poc.iam.gserviceaccount.com
gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID     --member="serviceAccount:gcp-poc@project-devops-poc.iam.gserviceaccount.com"     --role="roles/compute.admin"     --role="roles/compute.storageAdmin"     --role="roles/container.admin"  
gcloud iam service-accounts list
# Generate a key for your new service account
gcloud iam service-accounts keys create iam-key.json \
  --iam-account="gcp-poc@project-devops-poc.iam.gserviceaccount.com";

# for terrform 

update the json file key in root folder
update the project id and with your requirement
then terraform plan
terraform init
terraform apply it will lauch the cluster and 3 vm in each zone






# build the gcr image and push into gcr repo

gcloud auth configure-docker     us-central1-docker.pkg.dev
docker-credential-gcr configure-docker --registries=us-central1-docker.pkg.dev
git clone https://github.com/rootleveltech/rlt_terraform_k8s_test.git
cd rlt_terraform_k8s_test/
docker build -t rlt-test .
docker tag rlt-test  gcr.io/project-devops-poc/rlt-test
docker push  gcr.io/project-devops-poc/rlt-test

# Generate kubctl file 

gcloud container clusters get-credentials $(terraform output -raw kubernetes_cluster_name) --region $(terraform output -raw region)
or
gcloud container clusters get-credentials project-devops-poc-gke --region us-central1
replace with terraform value
us-central1  project-devops-poc-gke


# issue and updation in helm charts
# deployment
1
outdate api version is mention in manifest file 
for deployments i replaces the values from 
apiVersion: apps/v1beta2 to apiVersion: apps/v1
2
changes is probes path from /html to /
3 
update the gcr repo name and tag with respect to my gcr repo name which is
"gcr.io/project-devops-poc/rlt-test:latest"

# services
for Service i replace the fields as  "targetPort: http" to "targetPort: 80" it is not working.

# ingress

for ingress i changes the fields from apiVersion: networking.k8s.io/v1 to extensions/v1beta1
there is same sytax issues due to version issues i change the ingess update yaml syntax

# install new nginx controller ingress

i can use the gke on supported ingress due to the helm structer prefer nginx ingress controller

install new ingress controller 

helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace

  helm list -n ingress-nginx
optional
   helm uninstall ingress-nginx -n ingress-nginx



# helm installation commands
cd /rlt_terraform_k8s_test/charts
helm install rlt ./rlt-test
helm upgrade rlt ./rlt-test
helm template rlt ./rlt-test 


# testing
for testing when we launch the ingress controller it will create the  loadbalancer we can get network pulic ip and make the host in my local system 
with the that is mention in value.yml helm chart

Note there is issue the gcr repo is public so it will require to node pool to set the scope for the vm to have proper permissions form gks worker (in terraform) machine so will take time so i leave it for future 







