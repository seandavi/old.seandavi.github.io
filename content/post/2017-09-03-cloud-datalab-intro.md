---
title: 'Getting Started with Google Cloud Datalab'
author: Sean Davis
date: '2017-09-03'
slug: cloud-datalab-intro
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
---

# [Cloud DataLab]()


- Install the gcloud command-line tool by installing [Google Cloud SDK](https://cloud.google.com/sdk/downloads)
- Install the [datalab Cloud SDK component](https://cloud.google.com/sdk/docs/managing-components)
- Obtain account access to Google Cloud Platform

```{sh eval=FALSE}
gcloud auth login
```
To follow along, you'll need a Google Cloud Platform project that you own and which has the [Google Compute Engine API](https://console.cloud.google.com/apis/api/compute_component/?_ga=2.234205274.-515622365.1493905469) enabled. You can see a list of your projects together with their project IDs by running:

```{sh}
gcloud projects list
```

- Configure the gcloud tool to use your selected project:

```{sh}
gcloud config set core/project project-id
```

- Choose a zone

Select a zone for your Cloud Datalab instance (see [Choosing a region and zone](https://cloud.google.com/compute/docs/regions-zones/regions-zones#choosing_a_region_and_zone) for details). Using the `gloud` command line tool, you can list zones.

```{sh}
# list available zones
gcloud compute zones list
```
Run the following command to configure the gcloud tool to use your selected zone:

```{sh}
gcloud config set compute/zone zone
```

```{sh}
datalab --project isb-cgc-04-0020 create --disk-size-gb 200 babydatalab
```

{{< figure src="/img/datalab.png" title="Steve Francia" >}}

![datalab](/img/datalab.png)

```{sh}

