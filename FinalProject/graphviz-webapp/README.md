# graphviz-webapp
A simple Java Web application deployed as a Docker container and run as part of a [Kubernetes cluster]

## Getting Started

```
git clone https://github.com/kapgateshweta/cloud_computing.git
 
cd FinalProject/graphviz-webapp

```

To build a docker image:

```
docker build -t graphviz-webapp .
```

To run the container:

```
docker run -d -p 8080:8080 graphviz-webapp
```

To deploy the image to the Docker hub:

```
To deploy the image to the Google Container Registry:

```
# tag the image
docker tag graphviz-webapp gcr.io/<YOUR-PROJECT_ID>/graphviz-webapp
# push the image
gcloud docker push gcr.io/<YOUR-PROJECT_ID>/graphviz-webapp
```

