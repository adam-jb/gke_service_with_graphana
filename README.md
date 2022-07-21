

### Deploy service with Graphana logging


Graphana goes in a separate namespace to the main app

kubectx = tool to switch between kubectl clusters (aka contexts) faster

kubens = tool to switch between namespaces faster

Istio = service mesh. Includes a control plane. Allows you to transparently add capabilities like observability, traffic management, and security, without adding them to your own code

Istio has two components: the data plane and the control plane.

data plane = communication between services

control plane = implements your configuration



You deploy Grafana in a dedicated namespace in your cluster using a Helm chart and template

Helm Templates generate manifest yaml files, which k8 deploys


```
# set variables
export REGION=europe-west2-b
export MONITORING_NS=grafana
export CLUSTER=gke-flask-example
export APP_NS=hipstershop

export PROJECT_ID=$(gcloud config list --format 'value(core.project)' 2>/dev/null)
echo $PROJECT_ID

gcloud services enable \
	cloudshell.googleapis.com \
	cloudbuild.googleapis.com \
	containerregistry.googleapis.com \
	container.googleapis.com \
	cloudtrace.googleapis.com


# kubeconfig file with appropriate credentials and endpoint information to point kubectl at a specific cluster in Google Kubernetes Engine. (more on this: https://cloud.google.com/sdk/gcloud/reference/container/clusters/get-credentials)
gcloud container clusters get-credentials $CLUSTER --region=$REGION


# set context for kubectx and switch to it
kubectx cluster=gke_${PROJECT_ID}_${REGION}_${CLUSTER}
kubectx cluster


# switch namespace
kubectl create namespace $APP_NS
kubens $APP_NS


# enable istio sidecar injection
kubectl label namespace $APP_NS istio-injection=enabled


#### Apply the Istio manifests:


#### Create cluster


#### Deploy app to cluster


#### Install graphana
helm init
helm repo update
helm fetch stable/grafana --untar  # download graphana


helm repo add grafana https://grafana.github.io/helm-charts
helm repo update


# create graphana namespace
kubectl create ns $MONITORING_NS   

# make grafana manifest from template
helm template grafana --namespace $MONITORING_NS --name grafana > grafana.yaml


# deploy graphana
kubectl apply -f grafana.yaml -n $MONITORING_NS


# verify deployment
kubectl get pods -n $MONITORING_NS



# get graphana password
kubectl get secret \
    --namespace $MONITORING_NS grafana \
    -o jsonpath="{.data.admin-password}" \
    | base64 --decode ; echo






```


