---
title: "Google Cloud CheatSheet"
date: 2020-05-18T15:02:12-04:00
draft: true
---

See more in [link](https://cloud.google.com/ai-platform/deep-learning-vm/docs/tensorflow_start_instance)

## Step 1: install nd setup gcloud SDK 

[tutorial](https://cloud.google.com/compute/docs/gcloud-compute)

## Step 2: create vm

```bash
export IMAGE_FAMILY="tf-latest-cu92"
export ZONE="us-west1-b"
export INSTANCE_NAME="my-new-instance"
export INSTANCE_TYPE="n1-standard-8"
export DISKSIZE="120GB"
gcloud compute instances create $INSTANCE_NAME \
        --zone=$ZONE \
        --image-family=$IMAGE_FAMILY \
        --image-project=deeplearning-platform-release \
        --maintenance-policy=TERMINATE \
        --accelerator="type=nvidia-tesla-v100,count=8" \
        --machine-type=$INSTANCE_TYPE \
        --boot-disk-size=$DISKSIZE \
        --metadata="install-nvidia-driver=True"
```

## Step 3: control your vm 

- list vm: 
```bash 
gcloud compute instances list
``` 

- vm status
```bash 
gcloud compute instances describe $INSTANCE_NAME
``` 

- access vm 
```bash 
gcloud compute ssh $INSTANCE_NAME
```

- stop vm 
```bash 
gcloud compute instances stop $INSTANCE_NAME
```

- start vm 
```bash 
gcloud compute instances start $INSTANCE_NAME
```

- delete vm 
```bash 
gcloud compute instances delete $INSTANCE_NAME
```