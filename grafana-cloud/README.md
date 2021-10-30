# Grafana Cloud Setup

- Goals
  - Forward logs to Grafana Cloud via Fluent Bit
  - Forward Prometheus metrics to Grafana Cloud
  - Import Dashboards into Grafana Cloud

## Verify CSE-Labs access

- Go to <https://github.com/cse-labs/private-test>
  - If you see the readme, you have access
  - If not, go to <https://repos.opensource.microsoft.com/orgs/cse-labs> and join the CSE-Labs org
    - Retry the link above

## Create Grafana Cloud Account

- Go to <https://grafana.com> and create a free account

- Click on `My Account`
  - You will get redirected to this URL <https://grafana.com/orgs/yourAccountNameHere>
- In the left nav bar, click on `API Keys` (under Security)
- Click on `+ Add API Key'
  - Name your API Key (i.e. bartr-publisher)
  - Select `MetricsPublisher` as the role
  - Click on `Create API Key`
  - Click on `Copy to Clipboard` and save wherever you save your PATs
    - WARNING - you will not be able to get back to this value!!!

## Add your PAT to Codespaces

- Open this link in a new browser tab <https://github.com/settings/codespaces>
- Click `New Secret`
- Enter `GC_PAT` as the name
- Paste the Grafana Cloud PAT you just created in the value
- Click on `Select repositories`
  - Type `cse-labs` to limit the search
  - Select `cse-labs/kubernetes-in-codespaces`
  - Select any other repos you want to load this secret
- Click `Add Secret`

## Create a Codespace

- Navigate to <https://github.com/cse-labs/kubernetes-in-codespaces>
- Click the branch drop down
  - Select `gcloud`
- Click `Code`
  - Click `New Codespace`
  - Select cores
  - Click `Create Codespace`

## Create k3d Cluster

```bash

# create k3d cluster
make create

# deploy ngsa-app and webv
kubectl apply -f deploy/ngsa-memory
kubectl apply -f deploy/webv

# create the monitoring namespace
kubectl apply -f grafana-cloud/namespace.yaml

# wit for pods to start
kubectl get pods -A

# change to the grafana-cloud directory
cd grafana-cloud

```

## Set Environment Variables

- Set Prometheus values
  - From the `Grafana Cloud Portal`
    - <https://grafana.com/orgs/yourUserId>
  - Click `Details` in the `Prometheus` section
    - Copy your `Remote Write Endpoint` value
      - Export the value

      ```bash
      export GC_PROM_URL=pasteValue
      ```

    - Copy your `User` value
      - Export the value

      ```bash
      export GC_PROM_USER=pasteValue
      ```

- Set Loki Tenant ID
  - From the `Grafana Cloud Portal`
    - <https://grafana.com/orgs/yourUserId>
  - Click `Details` in the `Loki` section
    - Copy your `User` value
    - Export the value

    ```bash

    export GC_LOKI_USER=pasteValue

    ```

- Verify GC_* env vars are set

  ```bash

  env | grep GC_

  ```

  - Ouput should look like this

  ```text

  GC_PAT=xxxxxxxxxxxxxx==
  GC_LOKI_USER=######
  GC_PROM_URL=https://prometheus-prod-10-prod-us-central-0.grafana.net/api/prom/push
  GC_PROM_USER=######

  ```

- Deploy Fluent Bit

  ```bash

    kubectl apply -f fluentbit/account.yaml
    kubectl apply -f fluentbit/log.yaml

    # replace the credentials
    envsubst < fluentbit/gcloud-config.yaml | kubectl apply -f -

    kubectl apply -f fluentbit/fluentbit-pod.yaml

    # check pod
    kubectl get pods -n logging

    # check logs
    kubectl logs -n logging fluentbit

  ```

## Setup Prometheus

```bash

# change yourUserId
export GC_PROM_USER=yourUserId

# you may need to change this URL
export GC_PROM_URL=https://prometheus-prod-10-prod-us-central-0.grafana.net

# create Prometheus
envsubst < prometheus.yaml | kubectl apply -f -

```
