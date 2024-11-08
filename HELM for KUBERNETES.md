## What is Helm?
- de-facto standard for distributing software for Kubernetes
- Combination of:
	- Package Manager
	- templating engine
- Primary use case:
	- Application Deployment
	- Environment management
- Commands:
	- `helm install / helm upgrade'
	- `helm rollback`
- Features that matter the most for Helm:
	- Metadata(Chart/Release/Values), variables, Conditionals (if), Loops (range)


- First thing we think about Kubernetes is that it is a container orchestrator
	- containerized workloads and deploy on a number of compute resources
- Its not just that
- it provides an API for:
	- Declaring a set of resources
	- Observing and action upon those resources
- Customer Resource Definition (CRDS) allow applications to extend the default resource set
- Custom controllers observe and act upon CRDs
- Use cases:
	- Embed custom logic into "Operators" (CloudNativePG)
	- Manage TLS certificates (Cert-Manager)
	- Deploy infrastructure automatic reconciliation (Crossplane)
	- Anything you want
- Tooling
	- Kubebuilder
		- Build own CronJob via kubernetes tutorial
	- Operator SDK
	- Metacontroller
	- Build your with any k8s client
FOR DEBUGGING
Kubernetes provides a great troubleshooting flow chart:
https://learnk8s.io/troubleshooting-deployments

KUSTOMIZE
- built into kubectl
- uses base + overlay model
- easy to get started with
- struggles with:
	- Lists in YAML
	- Multi-line strings in yaml
KLUCTL
- uses templating model
- also has hooks
- integrates with helm and kustomize
- built in GitOps engine