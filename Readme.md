## Run applications

```shell
oc delete all -l app=http-s2i
oc new-app --name http-s2i httpd:2.4~https://github.com/adrien-legros/openshift-s2i-demo --context-dir html
oc expose svc http-s2i
```

```shell
oc delete all -l app=python-s2i
oc new-app --name python-s2i python:3.8-ubi8~https://github.com/adrien-legros/openshift-s2i-demo --context-dir python-dependencies
oc expose svc python-s2i
```

## Build workflow

The `s2i build` workflow is:

1. `s2i` creates a container based on the build image and passes it a tar file that contains:
    1. The application source in `src`, excluding any files selected by `.s2iignore`
    1. The build artifacts in `artifacts` (if applicable - see [incremental builds](#incremental-builds))
1. `s2i` sets the environment variables from `.s2i/environment` (optional)
1. `s2i` starts the container and runs its `assemble` script
1. `s2i` waits for the container to finish
1. `s2i` commits the container, setting the CMD for the output image to be the `run` script and tagging the image with the name provided.

### Incremental builds

`s2i` automatically detects:

* Whether a builder image is compatible with incremental building
* Whether a previous image exists, with the same name as the output name for this build

If a `save-artifacts` script exists, a prior image already exists, and the `--incremental=true` option is used, the workflow is as follows:

1. `s2i` creates a new container image from the prior build image
1. `s2i` runs `save-artifacts` in this container - this script is responsible for streaming out
   a tar of the artifacts to stdout
1. `s2i` builds the new output image:
    1. The artifacts from the previous build will be in the `artifacts` directory of the tar
       passed to the build
    1. The build image's `assemble` script is responsible for detecting and using the build
       artifacts

## S2I Flow

![sti-flow](./images/sti-flow.png "s2i workflow")