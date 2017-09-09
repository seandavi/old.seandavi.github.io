---
title: 'Distributed Machine Learning using Google Tensorflow: MNIST example'
author: Sean Davis
date: '2017-09-01'
slug: first-foray-into-google-cloud-machine-learning-distribute-tensorflow-example
categories:
  - Data Science
  - machine learning
tags:
  - tensorflow
  - machine learning
  - deep learning
  - cloud
  - Data Science
description: description
keywords:
  - key
  - words
topics:
  - topic 1
type: post
draft: true
---

# What is TensorFlow?

TensorFlow is an open source library for machine learning, developed
by researchers and engineers at Google. TensorFlow is designed to run
on multiple computers to distribute the training workloads, and
Google's Cloud Machine Learning Engine provides a managed service
where you can run TensorFlow code in a distributed manner by using
service APIs. TensorFlow often serves as a backend for other machine
learning frameworks such as keras. Finally, TensorFlow code can take
advantage of advanced CPU coprocessors and GPUs to speed computations,
sometimes by orders-of-magnitude relative to standard CPUs.

# Machine learning as a service

The Google Machine Learning engine is a a set of APIs that provide
access to managed services for machine learning. In particular, we can
write code in python using the tensorflow library and that code can
then run on the Google Machine Learning engine. By changing parameters
in the API calls, we can use more compute power to accomplish the
machine learning task more quickly. In addition, the service that
Google offers is both highly optimized and highly integrated with
other Google services such as Cloud Storage and DataFlow. Perhaps the
coolest aspect of using Google's machine learning service is that
trained models can be hosted as a managed service for prediction at
nearly arbitrary scale (for a price).

# The task and the tutorial

This tutorial shows you how to use a distributed configuration of
TensorFlow code in Python on Google Cloud Machine Learning Engine to
train a convolutional neural network model by using the MNIST
dataset. You use TensorBoard to visualize the training process and
Google Cloud Datalab to test the predictions.


The MNIST dataset enables handwritten digit recognition, and is widely
used in machine learning as a training set for image recognition.

In this tutorial, the term node refers to an application container
that runs parallel computations during training.

# Understanding neural networks

In computer programming, humans instruct a computer to solve a problem
by specifying each step using many lines of code. With machine
learning and neural networks, you instead get the computer to solve
the problem through examples.

A neural network is a mathematical function that can learn the
expected output for a given input from training datasets. The
following figure illustrates a neural network that has been trained to
output "cat" from a cat image.

A neural network model is a function that can be trained through
examples.

You can see that a neural network model consists of multiple layers of
calculation units, in which each layer has configurable
parameters. The goal of training the model is to optimize the
parameters to get results with the highest accuracy. The training
algorithm makes adjustments as it processes batches of training
77777777777777777777777777777datasets through the model. If you distribute the training process to
multiple computational nodes, you need a way to keep track of the
changing parameters to be shared by all nodes.

# Architecture of the distributed training

There are three basic strategies to train a model with multiple nodes:

- Data-parallel training with synchronous updates.
- Data-parallel training with asynchronous updates.
- Model-parallel training.

The example code in this tutorial uses data-parallel training with
asynchronous updates on Cloud ML Engine. In this case, a training job
is executed using the following types of nodes:

- Parameter server node 
  : Update parameters with gradient vectors from worker and chief work nodes.

- Worker node
  : Calculate a gradient vector from the training dataset.

- Chief worker node 
  : Coordinate the operations of multiple workers, in addition to working as one of the worker nodes.

Because you can use the data-parallel strategy regardless of the model
structure, it is a good starting point for applying the distributed
training method to your custom model. In data-parallel training, the
whole model is shared with all worker nodes. Each node calculates
gradient vectors independently from some part of the training dataset
in the same manner as the mini-batch processing. The calculated
gradient vectors are collected into the parameter server node, and
model parameters are updated with the total summation of the gradient
vectors. If you distribute 10,000 batches among 10 worker nodes, each
node works on roughly 1,000 batches.

Data-parallel training can be done with either synchronous or
asynchronous updates. When using asynchronous updates, the parameter
server applies each gradient vector independently, right after
receiving it from one of the worker nodes, as shown in the following
diagram.

Data-parallel training with asynchronous updates.

In a typical deployment, there are a few parameter server nodes, a
single chief worker node, and several worker nodes. When you submit a
training job through the service API, these nodes are automatically
deployed in your project.

The following diagram describes the architecture for running a
distributed training job on Cloud ML Engine and using Cloud Datalab to
execute predictions with your trained model.

Architecture used by the tutorial.

# Objectives

- Run the distributed TensorFlow sample code on Cloud ML Engine.
- Deploy the trained model to Cloud ML Engine to create a custom API
for predictions.
- Visualize the training process with TensorBoard.

## Costs

This tutorial uses billable components of Cloud Platform, including:

- Cloud ML Engine
- Google Cloud Storage
- Google Compute Engine
- Compute Engine Persistent Disk

The estimated price to run this tutorial, assuming you use every resource for an entire day, is approximately $1.20 based on this pricing calculator.

## Before you begin

Select or create a Cloud Platform project.
GO TO THE PROJECTS PAGE
Enable billing for your project.
ENABLE BILLING
Enable the Google Compute Engine and the Cloud Machine Learning APIs.
ENABLE THE APIS
Verifying the Google Cloud SDK components

Go to Cloud Shell.

OPEN CLOUD SHELL
List the models to verify that the command returns an empty list:

gcloud ml-engine models list
Verify that the command returns an empty list:

Listed 0 items.
If you've already worked with Cloud ML Engine, you'll get a list of all of the models associated with your account.

Downloading example files

Download the example files and set your current directory.

git clone https://github.com/GoogleCloudPlatform/cloudml-dist-mnist-example
cd cloudml-dist-mnist-example
Creating a Cloud Storage bucket for MNIST files

Create a regional Cloud Storage bucket to hold the MNIST data files
that are used to train the model.

```{sh}
PROJECT_ID=$(gcloud config list project --format "value(core.project)")
BUCKET="${PROJECT_ID}-ml"
gsutil mb -c regional -l us-central1 gs://${BUCKET}
```

Note: This tutorial puts resources in the us-central1 region. If you
choose to use a different region, be sure to change this value
everywhere to maintain performance.  Use the following script to
download the MNIST data files and copy them to the bucket.


```{sh}
./scripts/create_records.py
gsutil cp /tmp/data/train.tfrecords gs://${BUCKET}/data/
gsutil cp /tmp/data/test.tfrecords gs://${BUCKET}/data/
```

Training the model on Cloud Machine Learning Engine

Submit a training job to Cloud ML Engine.

```{sh}
JOB_NAME="job_$(date +%Y%m%d_%H%M%S)"
gcloud ml-engine jobs submit training ${JOB_NAME} \
    --package-path trainer \
    --module-name trainer.task \
    --staging-bucket gs://${BUCKET} \
    --job-dir gs://${BUCKET}/${JOB_NAME} \
    --runtime-version 1.2 \
    --region us-central1 \
    --config config/config.yaml \
    -- \
    --data_dir gs://${BUCKET}/data \
    --output_dir gs://${BUCKET}/${JOB_NAME} \
    --train_steps 10000
```

The `--train_steps` option specifies the total number of training batches.

You can control the amount of resources allocated for the training job
by specifying a scale tier with the configuration file
`config/config.yaml`. When the job starts running on multiple nodes, the
same Python code in the trainer directory that is specified with
--package-path parameter are deployed on all nodes. The files in the
trainer directory and their functions are listed in the table below:

File Description
----+-----------
setup.py | Setup script to install additional modules on nodes.
model.py | TensorFlow code to define the convolutional neural network model.
task.py | TensorFlow code to run the training task. In this example, Experiment API is used to run the training loop in a distributed manner.
----+-----------
Open the ML Engine page in the Google Cloud Platform Console to find the running job.

OPEN CLOUD ML ENGINE
Click the job ID to find a link to the log viewer. The example code shows progress in logs during the training. For example, each worker node shows a training loss value, which represents the total loss value for the dataset in a single training batch, at some intervals. In addition, the chief worker node shows a loss and accuracy for the test set. At the end of the training, the final evaluation against the test set is shown. In this example, the training achieved 99.3% accuracy for the test set.

Saving dict for global step 10008: accuracy = 0.9931, global_step = 10008, loss = 0.0315906
After the training, the trained model is exported in the storage bucket. You can find the storage path for the directory that contains the model binary by using the following command.

```{sh}
gsutil ls gs://${BUCKET}/${JOB_NAME}/export/Servo | tail -1
```

The output should look like this:

```{sh}
gs://${BUCKET}/job_[TIMESTAMP]/export/Servo/[JOB_ID]/
```

# Visualizing the training process with TensorBoard

After the training, the summary data is stored in gs://${BUCKET}/${JOB_NAME} and you can visualize them with TensorBoard.

Run the following command in Cloud Shell to start TensorBoard.

```{sh}
tensorboard --port 8080 --logdir gs://${BUCKET}/${JOB_NAME}
```

To open a
new browser window, select Preview on port 8080 from the Web preview
menu in the top-left corner of the Cloud Shell toolbar.  In the new
window, you can use TensorBoard to see the training summary and the
visualized network graph. Press Control+C to stop TensorBoard in the
Cloud Shell.

TensorBoard shows training summary and network graph.
Deploying the trained model for predictions

You deploy the trained model for predictions using the model binary.

Deploy the model and set the default version.

```{sh}
MODEL_NAME=MNIST
gcloud ml-engine models create --regions us-central1 ${MODEL_NAME}
VERSION_NAME=v1
ORIGIN=$(gsutil ls gs://${BUCKET}/${JOB_NAME}/export/Servo | tail -1)
gcloud ml-engine versions create \
    --origin ${ORIGIN} \
    --model ${MODEL_NAME} \
    ${VERSION_NAME}
gcloud ml-engine versions set-default --model ${MODEL_NAME} ${VERSION_NAME}
```

`MODEL_NAME` and `VERSION_NAME` can be arbitrary, but you can't reuse the same name. The last command is not necessary for the first version because it automatically becomes the default. It's a good practice to set the default explicitly.

It might take a few minutes for the deployed model to become ready. Until it becomes ready, it returns an HTTP 503 error against requests.
Test the prediction API by using a sample request file.

```{sh}
./scripts/make_request.py
```

This script creates a JSON file, named request.json, containing 10 test images for predictions.

Submit an online prediction request.

```{sh}
gcloud ml-engine predict --model ${MODEL_NAME} --json-instances request.json
```
You should get a response like this:

```{sh}
CLASSES  PROBABILITIES
7        [3.437006127094938e-21, 5.562060376991084e-16, 2.5538862785511466e-19, 7.567420805782991e-17, 2.891652426709158e-16, 2.2750016241705544e-20, 1.837758172149778e-24, 1.0, 6.893573298530907e-19, 8.065571390565747e-15]
2        [1.2471907477623206e-23, 2.291396136267388e-25, 1.0, 1.294716955176118e-32, 3.952643278911311e-25, 3.526924652059716e-36, 3.607279481567486e-25, 1.8093850397574458e-30, 7.008172489249426e-26, 2.6986217649454554e-29]
...
9        [5.124952379488745e-22, 1.917571388490136e-20, 2.02434602684524e-21, 2.1246177460406675e-18, 1.8790316524963657e-11, 2.7904309518969085e-14, 7.973171243464317e-26, 6.233734909559877e-14, 9.224547341257772e-12, 1.0]
CLASSES is the most probable digit of the given image, and PROBABILITIES shows the probabilities of each digit.
```
