# Cluster Information
| Command | Description |
| --------------------------------- | -------------------------------------------------- |
| kubectl cluster-info              | Shows the endpoint of the cluster and core service |
| kubectl get nodes                 | Lists all worker and master nodes in the cluster   |
| kubectl describe node <node-name> | Detailed info about a specific node                |

# Working with Pods
| Command | Description |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| kubectl get pods                                           | Lists all pods in the current namespace                      | 
| kubectl get pods -A                                        | Lists pods in all namespaces                                 |
| kubectl describe pod <pod-name>                            | Detailed info about a specific pod                           |
| kubectl logs <pod-name>                                    | View logs of a pod                                           |
| kubectl logs <pod-name> -c <container-name>                | View logs from a specific container in a pod                 |
| kubectl exec -it <pod-name> -- bash	                       | Execute a bash shell in a running pod (if bash is available) |

# Working with Deployments
| Command                                            | Description                              |
| -------------------------------------------------- | ---------------------------------------- |
| kubectl get deployments                            | Lists all deployments                    |
| kubectl describe deployment <name>                 | Describes a deployment in detail         |
| kubectl scale deployment <name> --replicas=<count> | Scale the number of pods in a deployment |
| kubectl rollout restart deployment <name>          | Restarts a deployment                    |
| kubectl rollout status deployment <name>           | View the rollout status                  |
| kubectl delete deployment <name>                   | Delete a deployment                      |

# Services & Networking
| Command                                                        | Description                           |
| -------------------------------------------------------------- | ------------------------------------- |
| kubectl get svc                                                | Lists all services                    |
| kubectl describe svc <service-name>                            | Details about a specific service      |
| kubectl port-forward svc/<svc-name> <local-port>:<remote-port> | Forward service port to local machine |

# Apply & Manage YAML Manifests
| Command                       | Description                                   |
| ----------------------------- | --------------------------------------------- |
| kubectl apply -f <file>.yaml  | Apply a configuration file                    |
| kubectl create -f <file>.yaml | Create resources from a file                  |
| kubectl delete -f <file>.yaml | Delete resources defined in a file            |
| kubectl diff -f <file>.yaml   | Show difference between live and local config |

# Namespaces
| Command                                                      | Description                       |
| -------------------------------------------------------------| --------------------------------- |
| kubectl get namespaces                                       | List all namespaces               |
| kubectl config set-context --current --namespace=<namespace> | Set the default namespace         |
| kubectl get pods -n <namespace>                              | List pods in a specific namespace |

# Config & Context
| Command                           | Description                  |
| --------------------------------- | ---------------------------- |
| kubectl config get-contexts       | List all kubeconfig contexts |
| kubectl config use-context <name> | Switch between clusters      |
| kubectl config view               | View kubeconfig file         |

# Debugging Tools
| Command                                   | Description                                                |
| ----------------------------------------- | ---------------------------------------------------------- |
| kubectl get events                        | Shows recent events in the cluster (helpful for debugging) |
| kubectl top pod                           | Show resource usage of pods (requires metrics-server)      |
| kubectl cp <pod-name>:<path> <local-path> | Copy files from pod to local or vice versa                 |
