![[Pasted image 20240812201326.png]]
Located in the Control Plane
- CCM (Cloud Controller Manager) - the interface between the cluster and the Cloud Provider
	- Logic for resources that live at that boundary are going to happen
- CM (Controller manager) - runs the various controllers that regulate the state of the cluster
- API - this is how you would interact with the K8 cluster. Makes calls to this API, in turn it will then make calls to the other various components
- ETCD (Data Store) - manages all of the resources that you deploy to the K8. Key-value store
- Sched - assign pods to new nodes, based on their current usage

Located in the data plane
- Kubelet - responsible for to spawn and manage the workloads of the nodes
	- In charge of checking the health of the nodes which then relays that info back to the API component
- K-proxy - in charge of setting up and maintaining the networking for the workloads
	- Not every workload will use k-proxy, third party apps now provide this


- K8 has three interfaces that it abstracts its functionality from
	- CRI the Container Runtime Interface
	- CNI the Container Network Interface
	- CSI the Container Storage Interface
- These allow for a more Modular system, where innovation can happen outside of the main K8 project
	- Can be easily pluuged in or swapped to achieve new functionality
## Resources

### Namespace: 
```
apiVersion: v1
kind: Namespace
metadata:
	name: non-default
```

- provides a mechanism to group resources within a cluster
	- There are 4 initial namespaces: ==default, kube-system, kube-node-lease, kube-public==
	- BY DEFAULT, namespaces do not act as a network/security boundary
- Store the definition of your resources in the codebase, instead of adding parameters to the cmd line
### Pod: 
```
apiVersion: v1
kind: Pod
metadata:
	name: nginx-minimal
spec:
	containers:
		- name: nginx
		  image: nginx:1.26.0
```
- the smallest deployable unit
- you will almost never create a pod directly (we are doing so for educational purposes)
- It is where our containers will be run
- a pod can contain multiple containers
- containers within a pod share networking and storage
- Init containers
- Sidecar containers
	- Do not run before the primary container, but alongside it
	- Uses: Network proxy, 
- There are MANY more configurations available:
	- Listening ports
	- Health probes
	- Resource requests/limits
	- Security Context
	- Environment variables
	- Volumes
	- DNS Policies
## KUBECTX Cheatsheet at 1:02:30
 Good resource to figure out how to build out Pods:
 https://github.com/BretFisher/podspec

For secure images grab from:
https://images.chainguard.dev/directory

## ReplicaSet
- adds the concept of replicas
- You will almost never create a replicate set directly
- Labels are the link between ReplicaSets and Pods
- great for maintaining a static definition of that pod

## Deployment
- Adds the concept of "rollouts" and "rollbacks"
- Used for long-running stateless applications
- can have a single definition of our applications deployment, we can modify fields within that definition, and kubernetes takes those changes and decides on how to apply those changes

## Service
- Serves as an internal load balancer, across replicas
- Use pod labels to determine which pods to serve
- Types:
	- ClusterIP: internal to cluster
	- NodePort: Listens on each node in cluster
	- LoadBalancer: Provisions external load balancer
```
apiVersion: v1
kind: Service
metadata:
	name: nginx-clusterip
spec:
	type: ClusterIP # Or NodePort Or LoadBalancer
	selector:
		app: nginx-pod-label
	ports:
		- protocal: TCP
	      port: 80
		  targetPort: 80
```

## Job
- Adds the concept of tracking "complettions"
- Used for execution of workloads that run to completion
- adds to the concept of a Pod
```
apiVersion: v1
kind: Pod
metadata:
	name: echo-date-minimal
spec:
	containers:
		- name: echo
		  image: busybox:1.36.1
		  command: ["date"] #It will just print the date
	restartPolicy: Never
```
## CronJob
- Adds the concept of "schedule"
- use for periodic execution of workloads that run to completion
- crontab.guru
	- helps with figuring out scheduling for cronjobs
- Cronjob execute by a schedule while jobs only execute on creation


![[Drawing 2024-08-24 14.56.12.excalidraw]]


## DaemonSet
- Runs a copy of the specified pod on all (or a specified subset of) nodes in the cluster
- Useful for applications such as
	- Cluster storage daemon
	- Log aggregation
	- node monitoring

## StatefulSet
- Similar to Deployment except:
	- Pods get sticky identity (pod-0, pod-1, etc)
	- Each pod mounts separate volumes
	- Rollout behavior is ordered
- Enables configuring workloads that require state management

## ConfigMaps
- Enables environment specific configuration to be decoupled form container images
- Two primary styles:
	- Property like `MY_ENV_VAR = "MY_VALUE"`
	- File like `conf.yml = <multi-line string>`

## Secrets
- Similar to ConfigMaps with one main difference:
	- Data is base64 encoded (this is to support binary data and is NOT a security mechanism)
- Because they are a separate resource type, they can be managed/controlled with specific authorization policies

```
apiVersion: v1
kind: Secret
metadata:
	name: base64-data
type: Opaque
data:
	foo: YmFy
```

```
apiVersion: v1
kind: Pod
metadata:
	name: secret-example
spec:
	containers:
		- name: nginx
		  image: nginx:1.26.0
		  volumeMounts:
			  - name: secret-base64-data
			    mountPath: /etc/config
		  env:
			  - name: ENV_VAR_FROM_SECRET
			    valueFrom:
				    secretKeyRef:
					    name: base64-data
					    key: foo
	volumes:
		- names: secret-base64-data
		  secret:
			  secretName: base64-data
```

## Ingress
- Enables routing traffic many services via a single external LoadBalancer
- Many options to choose from! Ingress-nginx, HAProxy, Kong, Istio, Traefik
- Only officially supports layer 7 routing (eg http/https) but some implementations allow for layer 4 (TCP/UDP) with additional configuration

## GatewayAPI
- Evolution of the Ingress API, not to be confused with an "API Gateway"
- Adds support for TCP/UDP
- Handles more advanced routing scenarios

## PersistentVolume & PersistentVolumeClaim
- Provides API for creating, managing, and consuming storage that lives beyond the live of an individual pod
- Access modes:
	- ReadWriteOnce (and NEW ReadWriteOncePod)
	- ReadOnlyMany
	- ReadWriteMany
- Reclaim Policy: Retain vs Delete
- PersistentVolumeClaim retention:
	- a field controls if and how PVCs are deleted during the lifecylce of a StatefulSet.
![[PersistenVol]]

## RBAC (ServiceAccount, Role, RoleBinding)
- Provides applications (or users) access to the Kubernetes API
- Access can be granted by namespace OR cluster wide
- Role applies to a namespace
- ClusterRole applies across the entire cluster

```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
	name: pod-reader
	namespace: default
rules:
	- apiGroups: [""]
	  resources: ["pods"]
	  verbs: ["get", "list", "watch"]
```

## Labels and Annotations
- Labels:
	- Key-value pairs used to identify and organize Kubernetes resources
	- Can be used to filter api-server queries (e.g. with kubectl)
- Annotations:
	- Key-value pairs used for non-identifying metadata
	- Used for things like configuration details, deployment history
	- Often used by tools to configure specific behaviors (e.g. ingress annotations)
## LOOK INTO THESE TOPICS
- LimitRange
- NetworkPolicy
- MutatingWebhookConfiguration
- ValidatingWebhookConfiguration
- HorizontalPodAutoscaler
- CustomResourceDefinition
