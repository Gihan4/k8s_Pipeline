# k8s_Pipeline

# Description
K8S_Pipeline is a revolutionary Flask web application designed to provide real-time Crypto prices to users, along with an efficient Redis database to track and analyze user interactions. The application is deployed on a highly scalable and reliable Kubernetes cluster hosted on Google Cloud Platform (GCP).

# Key Achievements
- Developed a feature-rich Flask web app to fetch and display real-time Crypto prices.
- Utilized Redis as the database backend to optimize data storage and enable precise entrance counting for improved user engagement analysis.
- Orchestrated the application using Kubernetes to ensure high availability and fault tolerance.
- Automated the CI/CD workflow using Jenkins for seamless Docker containerization and deployment to the Kubernetes cluster.
- Defined Kubernetes resources using YAML manifests, creating a declarative and version-controlled infrastructure setup.

# Technical Highlights
- Technology Stack: Flask, Redis, Kubernetes, Docker, Jenkins, YAML.
- Infrastructure: Hosted on Google Cloud Platform (GCP) Kubernetes cluster.
- Version Control: Managed using Git for effective collaboration and change tracking.
- Continuous Integration (CI): Configured Jenkins pipeline to trigger automated builds and tests on new code commits.
- Continuous Deployment (CD): Automatically deployed Dockerized app from Docker Hub to the Kubernetes cluster.
- Scalability and Load Handling: Designed the Kubernetes cluster to efficiently handle varying workloads and peak traffic.
- Monitoring: Implemented logging and monitoring tools for valuable insights into application behavior.
- Security: Adhered to best practices to secure Kubernetes cluster, application, and database.

# Lessons Learned
- Gained hands-on experience in building and deploying containerized applications using Kubernetes.
- Developed a comprehensive understanding of CI/CD principles and their significance in modern software development.
- Acquired valuable insights into using Jenkins for automating the development workflow.
- Enhanced skills in managing and optimizing a Redis database for high performance and data integrity.
- Cultivated expertise in using Docker as a containerization solution for packaging and distributing applications.

# Future Enhancements
- Integration with additional cryptocurrency exchanges to expand the range of supported Crypto prices.
- Implementing advanced data analytics and visualization tools for better insights into user behavior.
- Setting up an automated scaling mechanism within Kubernetes to handle traffic spikes seamlessly.
- Exploring security enhancements, such as role-based access control (RBAC) and network policies, to fortify the application's defenses.

# Conclusion
K8S_Pipeline showcases a robust and efficient CI/CD workflow, reflecting a dedication to delivering top-tier solutions. The successful deployment on a Kubernetes cluster in GCP, coupled with automation achieved through Jenkins, demonstrates an in-depth understanding of DevOps principles and tools. With valuable lessons learned and a roadmap for future enhancements, K8S_Pipeline underscores a commitment to staying at the forefront of cutting-edge DevOps practices.







Prerequisites:
- this ia a quick tutorial to set up before runnning the pipeline. this setup is required only once.
- notice: if you change the cluster, you shall redo the command <configure 'kubectl'>.

- Create a cluster in GCP Kubernetes Engine.
- Install `kubectl` on the local Jenkins machine. You can follow this guide for installing `kubectl` on Linux: [Install `kubectl` on Linux](https://gist.github.com/davidlzs/20237052e4b8a671b65e057c21d13d19#file-install_kubectl_on_linux-sh).
- Create a key file in GCP for authentication.
- Set the `GOOGLE_APPLICATION_CREDENTIALS` environment variable on the local host to the path of your `<key-file.json>`. `export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/key-file.json"`.
- install gcloud. you can follow this guide for installing 'gcloud' on linux: https://gist.github.com/devops-school/20d8e18d31fe69d7f02c9ce01acbca05
- run this command: <sudo apt-get install google-cloud-sdk-gke-gcloud-auth-plugin>
- Configure `kubectl` with cluster credentials on the local host using the command: `gcloud container clusters get-credentials <cluster-name> --zone <zone> --project <project-id>`.
- store the manifest yaml file in local jenkins.
- great! now you will have a configured workspace with a connected running cluster to jenkins.

or
- https://cloud.google.com/sdk/docs/install
- https://cloud.google.com/blog/products/containers-kubernetes/kubectl-auth-changes-in-gke
- gcloud auth login
- gcloud container clusters get-credentials CLUSTER_NAME

