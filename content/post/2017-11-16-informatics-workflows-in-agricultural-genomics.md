---
title: 'Agricultural genomics may benefit from human genomic data and software engineering'
author: Sean Davis
date: '2017-11-16'
slug: informatics-workflows-in-agricultural-genomics
categories:
  - Data Science
  - bioinformatics
tags:
  - bioinformatics
  - Data Science
description: "Agricultural genome informatics folks may find benefit from the growing tool sets for large-scale, portable, flexible, and reusable workflow and pipelines."
type: post
draft: false
aliases: ['/post/2017/11/agricultural-genomics-can-benefit-from-human-genomic-data-and-software-engineering/']

---

As a government employee, I have been given some fantastic opportunities
to interact with other government employees and agencies doing really 
important research in service to the country. Over the past couple of days, I have
been attending a great symposium to provide an updated 
[Blueprint for animal genetics and genomics](https://nifa.usda.gov/sites/default/files/resources/Blueprint%20for%20animal%20genetics%202008-17.pdf).
Discussion was wide-ranging, but largely focused on genomics, informatics,
and translation to and from phenotypes. High-throughput phenotyping (think
wearables for plants and cows and drone videos of cattle herds) seems like
a growth area. Creating basic genomics resources, such as reference genomes and variant databases from large populations, which are sometimes not available for livestock or
qauaculture species, is ongoing work. Data sharing,
data commons, data science, education, data reuse, etc. were all recurring themes. 
While I could say a lot about each of these topics and others (slides
from [my talk are here](https://seandavi.github.io/talk/2017/11/15/thoughts-on-an-agricultural-data-ecosystem/)), I was struck by a low-hanging fruit to 
point out to my USDA colleagues here, bioinformatic workflow systems.

There are *many* pipeline systems available, [listed here](https://github.com/pditommaso/awesome-pipeline). Adopting one of these pipeline systems
within a research group can be a transformative experience for the group
and I highly encourage spending the time and energy to do so. The 
portability of bioinformatics pipelines implemented using these tools 
varies quite a bit, as does the "cost" for transitioning current workflows
or developing new ones. I cannot speak for all of NCI, but I know a number of
groups using [snakemake](http://snakemake.readthedocs.io/en/stable/) who find
it a fantastic, capable, and flexible tool.

![](http://www.commonwl.org/CWL-Logo-Header.png)
![](https://software.broadinstitute.org/wdl/resources/img_shared/wdl-logo_white.png)


In thinking about how the USDA Agricultural Research Service (ARS--think of this
as the NIH of USDA) works, they are geographically distributed, so portability and sharing of workflows is a challenge they voiced as having to face (don't we all?). Recent work on 
[Common Workflow Language (CWL)](https://github.com/common-workflow-language/common-workflow-language) and
[Workflow Description Language (WDL)](https://software.broadinstitute.org/wdl/) make these description languages a good fit for developing pipelines
meant to be portably shared. Workflow engines such as [Cromwell](https://github.com/broadinstitute/cromwell) exist
for running these general workflows on multiple compute systems including
laptops, workstations, HPC clusters, and commercial cloud infrastructure. Individual
tasks in the workflow are wrapped in containers such as 
[Docker](https://www.docker.com/). Perhaps the agricultural genome informatics can find some value in and potentially add to the ecosystem of tools and workflows for genomics. 


