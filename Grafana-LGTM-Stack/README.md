LGTM Stack Deployment Steps
Here's a generalized sequence of commands to deploy the LGTM stack components using their respective Helm charts.

1. Add Helm Repositories
You'll need the Grafana and Prometheus Community Helm repositories.

helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

2. Prepare for Persistent Storage
Since your kubectl get pods -A output shows local-path-provisioner is running, you can use the storage class named local-path for persistent volumes. You will need to specify this in the values.yaml file for each component.

3. Install Loki (Logs) and Promtail (Agent)
Loki is a log aggregation system. Promtail is the agent that collects logs from your cluster's pods and sends them to Loki. The loki-stack chart includes both.

It's recommended to create a dedicated namespace, for example, monitoring.

# Create the monitoring namespace
kubectl create namespace monitoring

# Install Loki Stack
# The deployment will be monolithic by default, which is good for small clusters like k3s.
# We explicitly set the storage class for persistence.
helm install loki grafana/loki-stack \
  --namespace monitoring \
  --set loki.persistence.enabled=true \
  --set loki.persistence.storageClassName=local-path \
  --set promtail.enabled=true \
  --wait

4. Install Grafana (Visualization)
Grafana is the visualization layer. You'll install it and configure it to use Loki, Tempo, and Mimir as data sources later.

First, create a Kubernetes Secret for your admin credentials.

# Create a secret for Grafana admin credentials (username: admin, password: <YOUR_PASSWORD>)
kubectl create secret generic grafana-admin-credentials --namespace monitoring \
  --from-literal=admin-user=admin \
  --from-literal=admin-password='<A_SECURE_PASSWORD_HERE>'

Next, create a grafana-values.yaml file to enable persistence and use the secret:

cat <<EOF > grafana-values.yaml
persistence:
  enabled: true
  storageClassName: local-path
  size: 1Gi
admin:
  existingSecret: grafana-admin-credentials
EOF

Then, install Grafana:

helm install grafana grafana/grafana \
  --namespace monitoring \
  -f grafana-values.yaml \
  --wait

5. Install Mimir (Metrics) and Tempo (Traces)
Mimir (metrics) and Tempo (traces) are more complex and should ideally be installed with a tailored values.yaml to ensure they use the monolithic mode and local-path storage, similar to Loki.

For a basic, small-scale deployment, you'd typically proceed with a monolithic setup for these as well, making sure to configure persistence and the storage class.

Verification and Access
After installation, verify that all pods in the monitoring namespace are running.

kubectl get pods -n monitoring

You should see pods for loki, promtail, and grafana running.

To access Grafana, you can use port-forwarding from your control plane:

# Get the Grafana service name (it will likely be 'grafana')
# kubectl get svc -n monitoring

# Port forward the Grafana service to your local machine on port 3000
kubectl port-forward svc/grafana 3000:80 -n monitoring

Open your browser and navigate to http://localhost:3000.

Login with the username admin and the password you set in the secret.

From there, you would need to manually configure the Loki, Mimir, and Tempo data sources in Grafana to complete the integration. Loki's default service endpoint inside the cluster will typically be http://loki-stack.monitoring.svc.cluster.local:3100 (the name might vary slightly depending on the exact chart name).

Would you like me to provide the configuration for a monolithic Mimir or monolithic Tempo installation using Helm for your k3s cluster?


