# k8s_Pipeline
the pipeline script's stages include checking out the flask repository, building the Docker flask, pushing the image to DockerHub, and deploying it to the Kubernetes cluster. It assumes that the cluster is already provisioned and running.


prerequisites:
create a cluster in gcp k8s engine.
install kubectl to local jenkins. (https://gist.github.com/davidlzs/20237052e4b8a671b65e057c21d13d19#file-install_kubectl_on_linux-sh)
create key file in gcp
Set the GOOGLE_APPLICATION_CREDENTIALS environment variable: path of <key-file.json> on local host
Configure kubectl with cluster credentials on local host. gcloud container clusters get-credentials <cluster-name> --zone <zone> --project <project-id>

