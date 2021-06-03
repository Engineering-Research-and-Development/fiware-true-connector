## How to build a Docker image

Building a new Docker image for the TRUE Connector can be useful if you want to integrate your changes without relying
on Docker volumes.

The operation is quite easy after clone the source code of [ECC](https://github.com/Engineering-Research-and-Development/true-connector-execution_core_container)

```bash
cd true-connector-execution_core_container
docker build -f Dockerfile -t <registry-name/hub-user>/<repo-name>:<version-tag> .

Example:
docker build -f Dockerfile -t johndoe/execution_core_container:1.0 .
```

Be aware that using an existing Docker Hub username is required to push the image to Docker Hub