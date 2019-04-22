This is the Docker image that we use for Bitbucket pipelines.

# create a new version
```
rake build[1.1.0]
rake build # builds "latest" tag
```
# Push to Docker Hub
```
rake push[1.1.0]
rake push # latest
```
