#!/usr/bin/env make
.PHONY: run_website stop_website install_kind create_kind_cluster \
	install_kubectl create_docker_registry connect_registry_to_kind_network \
	connect_registry_to_kind create_kind_cluster_with_registry delete_kind_cluster \
	delete_local_registry delete_kind_cluster_and_registry install_ingress_controller \
	install_app

run_website:
	docker build -t explorecalifornia.com . && \
		docker run -p 5000:80 -d --name explorecalifornia.com --rm explorecalifornia.com

stop_website:
	docker stop explorecalifornia.com

install_kind:
	curl -L --output ./kind  https://github.com/kubernetes-sigs/kind/releases/download/v0.23.0/kind-linux-amd64 && \
		./kind --version

create_kind_cluster: install_kind install_kubectl create_docker_registry
	kind create cluster --name explorecalifornia.com --config ./kind_config.yaml || true && \
		kubectl get nodes


install_kubectl: 
	curl -fsSL https://get.jetpack.io/devbox | bash


create_docker_registry:
	if docker ps | grep -q 'local-registry'; \
	then echo "---> local-registry already created - skipping"; \
	else docker run --name local-registry -d --restart=always -p 5000:5000 registry:2; \
	fi

connect_registry_to_kind_network: #! make this not fail
	docker network connect kind local-registry || true 

connect_registry_to_kind: connect_registry_to_kind_network
	kubectl apply -f ./kind_configmap.yaml

create_kind_cluster_with_registry:
	$(MAKE) create_kind_cluster && $(MAKE) connect_registry_to_kind

delete_kind_cluster: 
	kind delete cluster --name explorecalifornia.com

delete_docker_registry:
	docker stop local-registry && docker rm local-registry

delete_kind_cluster_and_registry:
	$(MAKE) delete_kind_cluster && $(MAKE) delete_local_registry

install_ingress_controller:
	kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml && \
	sleep 5 && \
	kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s

install_app:
	helm upgrade --atomic --install explore-california-website ./chart