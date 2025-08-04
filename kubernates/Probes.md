### To pull images from local:

1. Start a local registry container:  
`docker run -d -p 5000:5000 --restart=always --name registry registry:2`  
2. Do docker images to find out the REPOSITORY and TAG of your local image. Then create a new tag for your local image :  
`docker tag <local-image-repository>:<local-image-tag> localhost:5000/<local-image-name>`  
If TAG for your local image is <none>, you can simply do:  
`docker tag <local-image-repository> localhost:5000/<local-image-name>`  
3. Push to local registry :
`docker push localhost:5000/<local-image-name>`
This will automatically add the latest tag to localhost:5000/<local-image-name>. You can check again by doing docker images.
---

## Startup Probe:
1. **When it runs:** This probe runs first when a container starts. It is designed to determine if the application within the container has successfully initialized
2. While the startup probe is running and has not yet succeeded, liveness and readiness probes are disabled.
3. No Traffic is sent unless startup probe succeeds.

## Liveness Probe:
1. **When it runs:** This probe runs periodically throughout the container's entire lifecycle, after the startup probe has succeeded.
2. It checks if the application within the container is still running and healthy. If the liveness probe fails, Kubernetes will restart the container to attempt to restore its healthy state.

## Readiness Probe:
1. **When it runs:** This probe also runs periodically throughout the container's entire lifecycle, after the startup probe has succeeded. (same as liveness probe)
2. It determines if the application is ready to serve traffic. If the readiness probe fails, Kubernetes will remove the container's IP address from the service endpoints, preventing new traffic from being routed to that container until it becomes ready again. **This does not restart the container**
