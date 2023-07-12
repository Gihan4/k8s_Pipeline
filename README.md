# k8s_Pipeline
The pipeline script's stages include checking out the Flask repository, building the Flask Docker image, pushing the image to DockerHub, and deploying it to the Kubernetes cluster. It assumes that the cluster is already provisioned and running.

Prerequisites:

- Create a cluster in GCP Kubernetes Engine.
- Install `kubectl` on the local Jenkins machine. You can follow this guide for installing `kubectl` on Linux: [Install `kubectl` on Linux](https://gist.github.com/davidlzs/20237052e4b8a671b65e057c21d13d19#file-install_kubectl_on_linux-sh).
- Create a key file in GCP for authentication.
- Set the `GOOGLE_APPLICATION_CREDENTIALS` environment variable on the local host to the path of your `<key-file.json>`.
- Configure `kubectl` with cluster credentials on the local host using the command:
