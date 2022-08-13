# gcp-terraform-gke creds setting
gcloud config list project <br />
gcloud config set project project-devops-poc <br />
echo $DEVSHELL_PROJECT_ID <br />
gcloud iam service-accounts create gcp_poc --display-name "gcp_poc service account" <br />
gcloud iam service-accounts list <br />
gcp-poc@project-devops-poc.iam.gserviceaccount.com
gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID     --member="serviceAccount:gcp-poc@project-devops-poc.iam.gserviceaccount.com"     --role="roles/compute.admin"     --role="roles/compute.storageAdmin"     --role="roles/container.admin"   <br />
gcloud iam service-accounts list <br />
# Generate a key for your new service account
gcloud iam service-accounts keys create iam-key.json \
  --iam-account="gcp-poc@project-devops-poc.iam.gserviceaccount.com";

# for terrform 

update the json file key in root folder <br />
update the project id and with your requirement <br />
then terraform plan <br />
terraform init <br />
terraform apply it will lauch the cluster and 3 vm in each zone <br />






# build the gcr image and push into gcr repo

gcloud auth configure-docker     us-central1-docker.pkg.dev <br />
docker-credential-gcr configure-docker --registries=us-central1-docker.pkg.dev <br />
git clone https://github.com/rootleveltech/rlt_terraform_k8s_test.git <br />
cd rlt_terraform_k8s_test/ <br />
docker build -t rlt-test . <br />
docker tag rlt-test  gcr.io/project-devops-poc/rlt-test <br />
docker push  gcr.io/project-devops-poc/rlt-test <br />

# Generate kubctl file 

gcloud container clusters get-credentials $(terraform output -raw kubernetes_cluster_name) --region $(terraform output -raw region) <br />
or <br />
gcloud container clusters get-credentials project-devops-poc-gke --region us-central1 <br />
replace with terraform value <br />
us-central1  project-devops-poc-gke


# issue and updation in helm charts
# deployment
1 <br />
outdate api version is mention in manifest file  <br />
for deployments i replaces the values from  <br />
apiVersion: apps/v1beta2 to apiVersion: apps/v1 <br />
2 <br />
changes is probes path from /html to / <br />
3 <br />
update the gcr repo name and tag with respect to my gcr repo name which is <br />
"gcr.io/project-devops-poc/rlt-test:latest" <br />

# services
for Service i replace the fields as  "targetPort: http" to "targetPort: 80" it is not working. <br />

# ingress <br />

for ingress i changes the fields from apiVersion: networking.k8s.io/v1 to extensions/v1beta1 <br />
there is same sytax issues due to version issues i change the ingess update yaml syntax <br />

# install new nginx controller ingress <br />

i can use the gke on supported ingress due to the helm structer prefer nginx ingress controller <br />

install new ingress controller  <br />

helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace

  helm list -n ingress-nginx <br />
optional <br />
   helm uninstall ingress-nginx -n ingress-nginx <br />



# helm installation commands
cd /rlt_terraform_k8s_test/charts <br />
helm install rlt ./rlt-test <br />
helm upgrade rlt ./rlt-test <br />
helm template rlt ./rlt-test <br />


# testing
for testing when we launch the ingress controller it will create the  loadbalancer we can get network pulic ip and make the host in my local system 
with the that is mention in value.yml helm chart
<br />

Note there is issue the gcr repo is public so it will require to node pool to set the scope for the vm to have proper permissions form gks worker (in terraform) machine so will take time so i leave it for future 







