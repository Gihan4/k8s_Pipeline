# k8s_Pipeline
the pipeline script's stages include checking out the flask repository, building the Docker flask, pushing the image to DockerHub, and deploying it to the Kubernetes cluster. It assumes that the cluster is already provisioned and running.


Prerequisites:

Create a cluster in GCP Kubernetes Engine.
Install kubectl on the local Jenkins machine. You can follow this guide for installing kubectl on Linux: Install kubectl on Linux.
Create a key file in GCP for authentication.
Set the GOOGLE_APPLICATION_CREDENTIALS environment variable on the local host to the path of your <key-file.json>.
Configure kubectl with cluster credentials on the local host using the command: gcloud container clusters get-credentials <cluster-name> --zone <zone> --project <project-id>.

