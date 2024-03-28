---
layout: post
title:  "Build a Singularity image from a Dockerfile"
date:   2024-03-28 12:00:00
categories:
    - blog
tags:
    - system
---


### Build the Docker Image

Ensure that you are in the directory containing your Dockerfile, then build the Docker image using the following command:
```sh
sudo docker build -t <image-name> .
```


### Run the Docker Image

Once the Docker image is built, you can run it using the following command:
```sh
sudo docker run <image-name>:<tag>
```


### Save the Docker Image

To save the Docker image as a tar file for later use, execute the following command:
```sh
sudo docker save <image-name>:<tag> <image-name>.tar
```


### Handle "no space left on device" Error

In case you encounter the "no space left on device" error while saving the Docker image, you can resolve it by creating a symbolic link to a directory with more space. Execute the following command:

```sh
sudo ln -s /path/to/large/tmp_directory /var/lib/docker/tmp
```


### Create the Singularity Image

Now, let's convert the Docker image into a Singularity image (.sif file). Set a temporary directory for Singularity to use during the conversion process:

```sh
export SINGULARITY_TMPDIR=/path/to/large/tmp_directory
```

Finally, build the Singularity image from the saved Docker image using the following command:

```sh
sudo -E singularity build <image-name>.sif docker-archive:<image-name>.tar
```

