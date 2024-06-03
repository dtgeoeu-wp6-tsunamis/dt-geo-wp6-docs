# **Upload and use Docker images on GitHub WP6 page**
GitHub provides detailed instructions on how to do load and use containers at the page [Working with container registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry). Since this page contains a lot of information, here, I describe step-by-step instructions that I used to upload a docker image on our WP6 page and how to pull an existing image.   

## **Requirements**
Both to upload and to use a docker image, you need to have Docker Desktop installed and an account on GitHub. To upload an image on our WP6 page, you also need to be a member of the WP6 organisation.

## **Upload your docker image**
Docker images are stored in the [packages page](https://github.com/orgs/dtgeoeu-wp6-tsunamis/packages) of the WP6 GitHub organisation. By default, the image will be uploaded as "private", meaning that only people who are part of the WP6 GitHub page will be able to see it and use it, but, if desired, it can be changed to "public" after having uploaded it.   

Assuming you already have a Docker image ready to be shared, below are the steps you need follow.   

**1. Generate a personal access token**   
First, you need to generate an access token with the scope of writing packages. Follow the [instructions provided by GitHub](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic).   
At Step 8 of those instructions you will need to select the scope of the token you are generating. Select `write:packages`, which will automatically select also `read:packages`, and `delete:packages`.   
Once you have generated your token, make sure to copy it somewhere, as it is needed at the next step.   

**2. Authenticate with your personal access token**   
Open a terminal and save your token in a variable:   
```
export CR_PAT=YOUR_TOKEN
```
where `YOUR_TOKEN` is the token you generated and copied at the previous step.   
Now you can authenticate to the GitHub Container registry `ghcr.io` with the command:   
```
echo $CR_PAT | docker login ghcr.io -u USERNAME --password-stdin
``` 
If the login was successful, you should see the message `Login Succeeded`.   

**3. Name your docker image**   
The image that you will upload needs to have a name that contains the path of the registry (i.e., ghcr.io/dtgeoeu-wp6-tsunamis/image_name). If you have not built your image yet, you can build it directly with that name:   
```
docker build -t ghcr.io/dtgeoeu-wp6-tsunamis/image_name .
```   

However, if you have already an image, you can create a new one with the right name using the tag option. To do that, you need to know the Image ID of the existing image and then use the tag as follows:   
```
docker tag imageID ghcr.io/dtgeoeu-wp6-tsunamis/image_name:latest
```

**4. Upload the docker image**   
Now you can push the image to GitHub:
```
docker push ghcr.io/dtgeoeu-wp6-tsunamis/image_name
```
You should now be able to see your image in the list of packages of our [WP6 packages page](https://github.com/orgs/dtgeoeu-wp6-tsunamis/packages)

## **Pull a docker image from GitHub**   
You can pull any of the images loaded on our packages page with the `docker pull` command:   
```
docker pull ghcr.io/dtgeoeu-wp6-tsunamis/image_name:latest
```
   
If you want to use singularity instead of Docker, you can download the docker image with the command:  
```
singularity pull --docker-login docker://ghcr.io/dtgeoeu-wp6-tsunamis/image_name:latest
```
You will be asked to insert username and password.   
A .sif file with the name of the image will be generated and stored in the folder from which you ran the command.   
Note that not all docker images are directly convertible to singularity images and some tweaking might be needed to use them.   