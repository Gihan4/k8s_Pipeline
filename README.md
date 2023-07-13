# k8s_Pipeline
The pipeline script's stages include checking out the Flask repository, building the Flask Docker image, pushing the image to DockerHub, and deploying it to the Kubernetes cluster. It assumes that the cluster is already provisioned and running.

Prerequisites:
- this ia a quick tutorial to set up before runnning the pipeline. this setup is required only once.
- notice: if you change the cluster, you shall rerun the command <configure 'kubectl'>.

- Create a cluster in GCP Kubernetes Engine.
- Install `kubectl` on the local Jenkins machine. You can follow this guide for installing `kubectl` on Linux: [Install `kubectl` on Linux](https://gist.github.com/davidlzs/20237052e4b8a671b65e057c21d13d19#file-install_kubectl_on_linux-sh).
- Create a key file in GCP for authentication.
- Set the `GOOGLE_APPLICATION_CREDENTIALS` environment variable on the local host to the path of your `<key-file.json>`.
- install gcloud. you can follow this guide for installing 'gcloud' on linux: https://gist.github.com/devops-school/20d8e18d31fe69d7f02c9ce01acbca05
- run this command: <sudo apt-get install google-cloud-sdk-gke-gcloud-auth-plugin>
- Configure `kubectl` with cluster credentials on the local host using the command: `gcloud container clusters get-credentials <cluster-name> --zone <zone> --project <project-id>`.
- store the manifest yaml file in local jenkins.
- great! now you will have a configured workspace with a connected running cluster to jenkins. 

