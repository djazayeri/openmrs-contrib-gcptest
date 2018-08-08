## Running locally
 
Prerequisites:

1. Install minikube (see https://github.com/kubernetes/minikube)
2. Run MySQL on some IP that can be accessed from the Kubernetes cluster
   * If using Cloud SQL make sure binary logging is _disabled_ for the initial installation

Steps:

1. Update the file [external-database.yaml](application/openmrs/external-database.yaml) with your MySQL DB's IP
1. Store DB credentials as a Kubernetes secret
	```
	kubectl create secret generic db-user-pass --from-literal username=openmrs --from-literal password=0penmr5
	```
1. Create persistent volumes for OMODs and OWAs
	```
	kubectl create -f application/openmrs/openmrs-pv.yaml
	kubectl create -f application/openmrs/openmrs-claims-minikube.yaml
	```
1. Create the OpenMRS deployment and service
	```
	kubectl create -f application/openmrs/openmrs-deployment.yaml
	``` 
1. Verify that it worked:
	```
	minikube dashboard
	# you can see your logs by going to the openmrs deployment 
	
	# browse to running OpenMRS	
	open http://$(minikube ip):30080/openmrs
	```

## Deploying to GKE

**This does not work, because I don't know how to find the IP address of the outbound
connection from GKE to Cloud SQL to authorize it. Documentation says to use the Cloud
SQL Proxy instead.**

Steps:

1. Go to https://console.cloud.google.com/ and create a project.
1. Go to Kubernetes Engine
1. Create Cluster
    * if you're using Cloud SQL, your zones should match
    * size 1 (keep down costs for testing)
    * do not need to attach a persistent disk (for testing) 
1. Connect -> Run in Cloud Shell (from this cluster in the UI)
1. Run these commands (remember to substitute real values!)
	```
	(press enter to run the command it automatically puts on the first line)
	kubectl create secret generic db-user-pass --from-literal username=openmrs --from-literal password=0penmr5
	git clone https://github.com/djazayeri/openmrs-contrib-gcptest.git
	cd openmrs-contrib-gcptest/kubernetes/
	kubectl create -f application/openmrs/external-database.yaml
	kubectl create -f application/openmrs/openmrs-pv.yaml
	kubectl create -f application/openmrs/openmrs-claims-gke.yaml
	kubectl create -f application/openmrs/openmrs-deployment.yaml
	```