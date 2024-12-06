
https://cloud.google.com/artifact-registry/docs/docker


## Working with a docker image on GCloud
- To get this working, Enable the Artifact Registry API on Gcloud console
- I did this all on the local shell and will copy and paste the commands here
- First create a Docker repository on gcloud:
	- `gcloud artifacts repositories create quickstart-docker-repo --repository-format=docker --location=us-west1 --description="Docker repository" --project=docker-practice-443904`
- Verify that the repo was made:
	- ````
gcloud artifacts repositories list \    --project=docker-practice-443904`

- configure the authentication on your local machine:
	- `gcloud auth configure-docker us-west1-docker.pkg.dev`
- Grab a image to push into the repo:
	- `docker pull us-docker.pkg.dev/google-samples/containers/gke/hello-app:1.0`
		- us-docker.pkg.dev is the hostname of the container which is stored in the Artifact Registry Docker repo
		- google-samples is the project ID
		- containers is the repo ID
		- /gke/hello-app is the path of the image in the repo
- tag the image to the repo:
	- `docker tag us-docker.pkg.dev/google-samples/containers/gke/hello-app:1.0 us-west1-docker.pkg.dev/docker-practice-443904/quickstart-docker-repo/quickstart-image:tag1`
		- us-west1 is the repo location
		- us-west1-docker.pkg.dev is the hostname of the Docker repo
		- docker-practice-443904 is the project ID
		- quickstart-docker-repo is the repo ID
		- quickstart-image is the image name that you want to use in the repo
		- tag1 is the tag your are adding to the Docker image
- push the image to the artifact registry:
	- `docker push us-west1-docker.pkg.dev/docker-practice-443904/quickstart-docker-repo/quickstart-image:tag1`
- to pull the image, from the artifact registry, to your local machine:
	- `docker pull us-west1-docker.pkg.dev/docker-practice-443904/quickstart-docker-repo/quickstart-image:tag1`
- to clean up and delete the docker repo:
	- `gcloud artifacts repositories delete quickstart-docker-repo --location=us-west1`