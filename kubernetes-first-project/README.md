# Docker
## Docker run container
`docker run --rm --name califcont -p 5000:80 somethingcalif`
## Remove container
`docker rm <id of container> --force` 

# Create cluster
`sudo kind create cluster --name explorecalifornia.com`

# Make commands
`make create_kind_cluster_with_registry` 

# Helm
`helm show all ./chart/` - lists the Chart and values
`helm template ./chart` - renders templates
`helm uninstall explore-california-website` - uninstalls helm chart


# Shut down
`wsl --shutdown`

# Devbox
`devbox install` to install what's in devbox.json
`devbox shell` to activate devbox environment

# Docker
Tag docker image
`docker tag explorecalifornia.com localhost:5000/explorecalifornia.com`
Push to local registry
`docker push localhost:5000/explorecalifornia.com`

Remove images related to app

`docker images | grep explorecalifornia | awk '{print $3}' | xargs docker rmi -f` 

# Kubectl
`kubectl create deployment --dry-run=client --image localhost:5000/explorecalifornia.com explorecalifornia.com --output=yaml > deployment.yaml`

Get pods with selector
`kubectl get pods -l app=explorecalifornia.com` 

Apply deployment
`kubectl apply -f deployment.yaml`

Port-forward
`kubectl port-forward deployment/explorecalifornia.com 8080:80`

Delete all resources for namespace
`kubectl delete all -l app=explorecalifornia.com` 

Describe resource
`kubectl describe pod -l app=explore-california-website`