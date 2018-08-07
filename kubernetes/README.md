## Running locally
 
Prerequisites:

1. Install minikube (see https://github.com/kubernetes/minikube)
2. Run MySQL on some IP that can be accessed from the Kubernetes cluster
   * If using Cloud SQL make sure binary logging is _disabled_ for the initial installation

Steps:

1. Update the file [external-database.yaml](application/openmrs/external-database.yaml) with your MySQL DB's IP
1. Store DB credentials as a Kubernetes secret
	```
	echo -n 'openmrs' > ./username.txt
	echo -n '0penmr5' > ./password.txt
	kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt
	```
1. Create persistent volumes for OMODs and OWAs
	```
	kubectl create -f application/openmrs/openmrs-module-pv.yaml
	kubectl create -f application/openmrs/openmrs-owa-pv.yaml
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
