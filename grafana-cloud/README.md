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
make all

# delete fluent bit and prometheus
# we will deploy new versions of these
kubectl delete -f deploy/fluentbit
kubectl delete -f deploy/prometheus

# check your pods
kubectl get pods -A

# change to the grafana-cloud directory
cd grafana-cloud

```
