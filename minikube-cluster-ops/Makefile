.ONESHELL: # Applies to every targets in the file!



# stand-alone
# recipe:-1
# List all possible recipes
.PHONY: list
list:
		@LC_ALL=C $(MAKE) -pRrq -f $(firstword $(MAKEFILE_LIST)) : 2>/dev/null | awk -v RS= -F: '/(^|\n)# Files(\n|$$)/,/(^|\n)# Finished Make data base/ {if ($$1 !~ "^[#.]") {print $$1}}' | sort | egrep -v -e '^[^[:alnum:]]' -e '^$@$$'


PWD = $(shell pwd)
PROJECT_HOME = $(PWD)
MINI-K8S = minikube
MINI-K8S-PROFILE-NAME = xor-dev-minikube
K8S-CONTROLLER = kubectl
SANITY_CHECK_NODE_NAMESPACE = sanity-check-hello-node
SANITY_CHECK_NODE_DEPLOYMENT_NAME = hello-mini-k8s

# stand-alone
# recipe:0
# showing location for traceablity
hello-dev-minikube:
	@printf "%s\n" "hello minikube ops, you are operating minikube from below directory:";
	@printf "%s\n" $(PROJECT_HOME);

# stand-alone
# recipe:1
# Destory the existing minikube
destroy-minikube: stop-minikube-cluster delete-minikube-cluster

.PHONY:
	start-minikube
	enable-minikube

# stand-alone
# recipe:1-a
stop-minikube-cluster:
	@printf "%s\n" "stoping minikube..." && \
	$(MINI-K8S) stop -p $(MINI-K8S-PROFILE-NAME)

#recipe:1-b
delete-minikube-cluster:
	@printf "%s\n" "deleting minikube's container..." && \
	$(MINI-K8S) delete -p $(MINI-K8S-PROFILE-NAME)

# stand-alone
# recipe:2
# Re-create minikube
spin-up-minikube: start-minikube switch-dev-minikube-profile enable-minikube-addons open-minikube-dashboard

.PHONY:
	start-minikube
	switch-dev-minikube-profile	
	enable-minikube-addons
	open-minikube-dashboard
	
# stand-alone
# recipe:2-a
start-minikube:
	@printf "%s\n" "Starting minikube..." && \
	$(MINI-K8S) start -p $(MINI-K8S-PROFILE-NAME)


# stand-alone
# recipe:2-b
switch-dev-minikube-profile:
	$(MINI-K8S) profile $(MINI-K8S-PROFILE-NAME)

#recipe:2-c
enable-minikube-addons:
	@printf "%s\n" "Enabling addons..." && \
	$(MINI-K8S) addons enable ingress && \
	$(MINI-K8S) addons enable metrics-server

#recipe:2-d
open-minikube-dashboard:
	@printf "%s\n" "Opening minikube dashboard..." && \
	$(MINI-K8S) -p $(MINI-K8S-PROFILE-NAME) dashboard

# stand-alone
# recipe:3
# Deploy a sample application to minikube and expose it as service
ignite-minikube-sanity-check: deploy-hello-node switch-namespace-context-to-hello-node
sanity-check-deployment-As-A-Service: expose-sanity-check-deployment
# stand-alone
#recipe:4
offer-hello-node-service-to-consumers:
	$(MINI-K8S) service $(SANITY_CHECK_NODE_DEPLOYMENT_NAME) -n $(SANITY_CHECK_NODE_NAMESPACE)

list-minikube-profile:
	$(MINI-K8S) profile list

why-its-on-some-ipAndPort:
	@printf "%s\n" "The IP of node can be found here..." && \
	kubectl get nodes -o wide && \
	kubectl get service hello-mini-k8s --output='jsonpath="{.spec.ports[0].nodePort}"'

# stand-alone
# recipe:5
# roll out the sanity check application from minikube
# TODO make it more modularized and readability
roll-out-hello-node-deployment: switch-namespace-context-to-hello-node delete-hello-node-deployment clear-all-objects delete-hello-node-namespace report-minikube-sanity-check

.PHONY :
	deploy-hello-node
	expose-sanity-check-deployment
	delete-hello-node-deployment
	clear-all-objects
	delete-hello-node-namespace
	report-minikube-sanity-check
	clearscr
	k8s-version-header-print
	k8s-version 
	switch-namespace-context-to-hello-node
	sanity-check-node-deployment-status-print-all
	sanity-check-node-configuration-view
	sanity-check-node-events-view

.SILENT:
	deploy-hello-node
	expose-sanity-check-deployment
	delete-hello-node-deployment
	clear-all-objects
	delete-hello-node-namespace
	report-minikube-sanity-check
	clearscr
	k8s-version-header-print
	k8s-version
	switch-namespace-context-to-hello-node
	sanity-check-node-deployment-status-print-all
	sanity-check-node-configuration-view
	sanity-check-node-events-view


#recipe:3-a
deploy-hello-node:
	@printf "%s\n" "starting minikube's sanity check by deploying hello-node..." && \
	$(K8S-CONTROLLER) create ns $(SANITY_CHECK_NODE_NAMESPACE) && \
	$(K8S-CONTROLLER) create deployment $(SANITY_CHECK_NODE_DEPLOYMENT_NAME) --namespace=$(SANITY_CHECK_NODE_NAMESPACE) --image=gcr.io/google-samples/hello-app:1.0

expose-sanity-check-deployment:
	@printf "%s\n" "exposing minikube's sanity check node deployment as service..." && \
	$(K8S-CONTROLLER) expose deployment $(SANITY_CHECK_NODE_DEPLOYMENT_NAME) --type=NodePort --port=8080

delete-hello-node-deployment:
	@printf "%s\n" "Deleting minikube's sanity check deployment..." && \
	kubectl delete deployment $(SANITY_CHECK_NODE_DEPLOYMENT_NAME)

clear-all-objects:
	@printf "%s\n" "Deleting minikube's sanity check deployment..." && \
	kubectl delete all --all

delete-hello-node-namespace:
	kubectl delete namespace $(SANITY_CHECK_NODE_NAMESPACE)

# stand-alone
# recipe:6
report-minikube-sanity-check:	clearscr k8s-version-header-print k8s-version switch-namespace-context-to-hello-node sanity-check-node-deployment-status sanity-check-node-configuration-view sanity-check-node-events-view

#recipe:3-b-1
clearscr:
	clear

#recipe:3-b-2
k8s-version-header-print:
	@printf "%s\n"
	@printf "%s\n" "Kubernetes version running inside minikube cluster:"
	@printf "%s\n"

#stand-alone
#recipe:3-b-3
k8s-version:
	$(K8S-CONTROLLER) version --output=yaml

#stand-alone
#recipe:3-b-4
switch-namespace-context-to-hello-node:
	@printf "%s\n" "Switching to sanity check namespace..." && \
	$(K8S-CONTROLLER) config set-context --current --namespace='$(SANITY_CHECK_NODE_NAMESPACE)'

#stand-alone
#recipe:3-a-5
sanity-check-node-deployment-status:
	@printf "%s\n"
	@printf "%s\n" "hello-node deployment running status:"
	@printf "%s\n"
	$(K8S-CONTROLLER) get all

#recipe:3-a-6
sanity-check-node-configuration-view:
	@printf "%s\n"
	@printf "%s\n" "hello-node configuration view:"
	@printf "%s\n"
	$(K8S-CONTROLLER) config view

#recipe:3-a-7
sanity-check-node-events-view:
	@printf "%s\n"
	@printf "%s\n" "hello-node events happened till NOW:"
	@printf "%s\n"
	$(K8S-CONTROLLER) get events

#ingress
install-ingress-controller-nginx:
	@printf "%s\n" "Installing the latest ingress controller..." && \
	$(K8S-CONTROLLER) apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.0/deploy/static/provider/cloud/deploy.yaml

install-ingress-to-access-hello-mini-k8s:
	@printf "%s\n" "Installing the ingress ...." && \
	$(K8S-CONTROLLER) apply -f $(PROJECT_HOME)/hello-mini-k8s-ingress.yaml

delete-ingress-to-access-hello-mini-k8s:
	$(K8S-CONTROLLER) delete ingress hello-mini-k8s-ingress -n sanity-check-hello-node