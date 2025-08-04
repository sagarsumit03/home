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
