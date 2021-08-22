# OpenSearch - Kubernetes

## Prerequisite

Install [minikube](https://v1-18.docs.kubernetes.io/docs/tasks/tools/install-minikube/) and [Docker](https://docs.docker.com/get-docker/).

Setup minikube:

```bash
minikube config set driver docker # set Docker as minikube driver
minikube config set cpus 4 # adjust depending on your needs
minikube config set memory 8000MB
```

## Setup certificates

A script is provided to help you generate some certificates (`setup_certs.sh`). Run it. Then replace the values inside the `certs-secrets` folder with the values of the certificates you just generated.

Then create the secrets:

```bash
kubectl apply -f certs-secrets/
```

## OpenSearch

Choose the number of master and data nodes you want in your cluster and modify the replicas count in the `opensearch/master.yml` and `opensearch/data.yml` file.

If you choose to have more than 1 master node, be sure to change the `cluster.initial_master_nodes` options in `opensearch/configmap.yml`.

> For example, if you decide to have 3 master nodes, you will need to put:
>
> ```yml
> cluster.initial_master_nodes: opensearch-master-0,opensearch-master-1,opensearch-master-2
> ```

Choose the port where you want to access opensearch in the `spec.ports[0].nodePort` options in `opensearch/service.yml`. (by default it will be accessible on port 30002)

You can now launch your opensearch cluster

```bash
kubectl apply -f opensearch/
```

Make sure to also launch the security-admin job to initialize the security plugin.

```bash
kubectl apply -f security-admin
```

## OpenSearch Dashboards

Choose the number of replicas and set the replicas count accordingly in `dashboards/dashboards.yml`.

Choose the port where you want to access dashboards in the `spec.ports[0].nodePort` options in `dashboards/service.yml`. (by default it will be accessible on port 30003)

```bash
kubectl apply -f dashboards/
```

## Access services

Use the minikube command: `minikube service list` to get the public IP address of Opensearch and Dashboards.
