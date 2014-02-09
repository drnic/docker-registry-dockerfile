# Dockerfile & config for docker-registry

This project contains a Dockerfile and an example config file to run the docker-registry in standalone mode (no reference to the public Docker Index for authentication). The uploaded images are stored in the host folder at `cache/` so they survive restarts of the docker container itself.

## Usage

```
$ git submodule update --init
$ docker build -t <yourname>/docker-registry:standlone .
$ IMAGE_ID=$(docker images | head -n 2 | tail -n 1 | awk '{print $3}')
$ REGISTRY_ID=$(docker run -d -p 5000:5000 -v $(pwd)/cache:/registry $IMAGE_ID)
```

Now you can upload your newly created image (from `docker build -t`) to your newly running docker registry.

```
$ docker push localhost:5000/docker-registry
The push refers to a repository [localhost:5000/docker-registry] (len: 1)
Sending image list
Pushing repository localhost:5000/docker-registry (1 tags)
511136ea3c5a: Image successfully pushed 
f323cf34fd77: Image successfully pushed 
eb601b8965b8: Pushing [>                                                  ] 1.671 MB/177.6 MB 22s
...
```

To show that the registry contents themselves survive restarting the docker-registry:

```
$ docker stop $REGISTRY_ID
fb0e7bc41f2fcd665e2adf6281cbfc3ab89c1eeee2702179b725fe64bb096332
$ REGISTRY_ID=$(docker run -d -p 5000:5000 -v $(pwd)/cache:/registry $IMAGE_ID)
$ docker push localhost:5000/docker-registry
The push refers to a repository [localhost:5000/docker-registry] (len: 1)
Sending image list
Pushing repository localhost:5000/docker-registry (1 tags)
511136ea3c5a: Image already pushed, skipping 
f323cf34fd77: Image already pushed, skipping 
eb601b8965b8: Image already pushed, skipping 
3282b6e88440: Image already pushed, skipping 
a48bb0b5c537: Image already pushed, skipping 
b2fc0f853aa1: Image already pushed, skipping 
Pushing tags for rev [e231de292e28] on {http://localhost:5000/v1/repositories/docker-registry/tags/default}
e231de292e28: Image already pushed, skipping 
```


