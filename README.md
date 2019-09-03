
# Clouds for Research

Cloud computing is something many people talk about, but don't necessarily understand. Cloud computing environments are pay-for-use services offered by companies big and small to make it easy to have internet connected servers which can be used for many business applications. Cloud providers range from IaaS (Infrastructure as a Service) to SaaS (Software as a Service) with PaaS (Platform as a Service) in-between. This document is meant to provide a bit of demystification around cloud services and how they can be utilised for research purposes. In this document, we will largely focus on IaaS, further explained below. This work is supported by Genomics Aotearoa, but is generally applicable in many research areas. 

## Cloud Jargon 

Jargon abounds in discussions of computational clouds. While this document isn't meant to be a comprehensive jargon glossary, it will work to help you build a mental model for the various components that go into cloud environment. We're going to focus on the part of cloud environments which allows us to create "Virtual Machines" (VMs) where you pay-as-you-go to access a computer of a certain size (CPUs, Memory, disk). This is generally called Infrastructure as a Service or (IaaS) and can be a good early stepping stone to scale your computational abilities for your research group or company. The advantages of using the cloud are that it is easier to use than running your own servers locally and you will only incurring incremental costs. If you're doing large-scale high performance computing using 10s-100s of compute nodes, you may want to consider a custom hardware solution. However, there is a lot of productive science and business which can be done using cloud computing environments. This document hopes to give you some stepping stones and a bit of vocabulary necessary to consider how a cloud approach might fit your needs. 

###  Cloud types

- IaaS - Infrastructure as a Service (hardware environments) - Services which give you the most flexibility to run commodity compute. You get virtual machines in the cloud with console (SSH) access.
    - Amazon Web Services, 
    - Google Cloud, 
    - Microsoft Azure, 
    - Digital Ocean
- PaaS - Platform as a Service (flexible/configurable software environments) - These abstract away the specifics of server hardware and give you  a development environment to build against. 
    - OpenShift, 
    - Salesforce, 
    - AWS Elastic Beanstalk, 
    - Heroku
- SaaS - Software as a service (packaged software solutions) - Fully integrated applications and services which are pay-for-use
    - Github,
    - Trello 
    - Office365


### Cloud components

#### Virtual Machines 

Virtual machines are computers run in the cloud that you can get remote access too. Usually they are accessed using SSH, ( Secure SHell). You can purchase access by the hour (sometimes billed by the minute or second) to a computer of some size. Size being measured in memory, cpu and storage capabilities. There is a dizzying array of possible configurations and options in this space, but for relatively little cost, you can move workflows to cloud platforms. While you can bring up Windows computers in cloud environments, generally the lowest cost solution is to bring up a Linux environment. Virtual machine capacity is far more expensive than the raw cost of a server from say Dell or HP, but the savings in terms of accessibility, reliability and saved IT salaries can balance the equation. 

| Platform | Name |
| -------- | -------- |
| AWS     | EC2 (Elastic Compute Cloud) |
| Google | GCE (Google Compute Engine) |
| Microsoft | Virtual Machines | 

Virtual machine platforms tend to have a dizzying array of possible options, and early on you should probably just choose the "Standard" VM types. Other niche VM types add faster network and disk I/O (Input/Output), faster CPUs, faster memory and the availability of GPUs and other services. If your computations aren't time sensitive, there is also the opportunity to run what are called "spot-instances" which are VMs which run only when other demand in the cloud environment is low. When you bring up a cloud VM, you'll have many choices of what Linux environment to bring up.

Depending on your style of research computation, you may want a Virtual Machine which is always on, or surges of 10s-100s of machines to crank through a particular workflow. It is important to balance cost/benefits of each kind of configuration and understand what kinds of costs you might incur. Each cloud platform has various calculator environments FIXME links which can help you to model the possible costs. If you do have always-on machines, some cloud environments allow you to save on costs by paying in advance. This can significantly reduce costs for always-on VMs. 

##### Operating System 

Speaking of Linux, some popular distributions are Ubuntu, Debian and CentOS, with Ubuntu probably having the best balance of modern packages and ease of use. For scientific workflows Ubuntu is a reasonable recommendation unless you know specifically that you would benefit from CentOS or Debian for your application, choosing Ubuntu will give you reasonable abilities and flexibility.

| OS | Description | 
| -- | --------- |
| Debian | A long-time and very stable Linux environment. Packages are updated less quickly than in Ubuntu| 
| Ubuntu | A popular desktop and server distribution, many many packages work in this environment | 
| CentOS | An open source version of RedHat Linux, with specific hardening for Corporate IT environments | 

There are plenty of other distributions and whole operating systems out there (FreeBSD, OpenBSD etc). We will keep this guide simple by not going into those. This guide will stick to Linux. 

###### Cloud Start Script 

When you're constantly bringing up new virtual machines in cloud environments, you'll want to be able to get the set of software, packages and data you need installed on the VMs each time you start them. Most cloud environments allow you to specify a script to run as soon as the VM has started. These scripts can be good to have sitting in a public GitHub page so that the VM is able to pull down the script and run it to get the set of tools and packages installed that you need. 

[Cloud-init](https://cloudinit.readthedocs.io/en/latest/) is available on all three ([Google](https://cloud.google.com/container-optimized-os/docs/how-to/create-configure-instance#using_cloud-init), [AWS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html#user-data-cloud-init), and [Microsoft](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init)). See the documentation for the various ways to setup a cloud-init script to build your servers. With cloud-init configurations, things like user accounts and packages you'd like to have installed on the Virtual Machine when it turns-on can be configured. This takes a bit of trial-and-error to get right the first time, but once you have a script its great to be able to have all the software and configuration you need to be productive. 



####  Storage 

##### Bucket Storage 

Bucket storage is a model where storage assets can be GET or PUT (verbs from the HTTP standard) from a storage location.

Bucket Storage names on major cloud platforms:

| Platform | Name |
| -------- | -------- |
| AWS     | S3 (Simple Storage Service) Bucket |
| Google | GCS (Google Cloud Storage) |
| Microsoft | Blob Storage | 

Bucket stores can be great ways to store large amounts of data and AWS and Google allow for tiered bucket storage where you can give up accessiblity or redundancy (number of copies of the data) to save on cost. The common theme in these bucket stores is that they hold groups of files within a named bucket. Within that bucket you have keys, the filenames and values, the contents of those files. With this simple abstraction it is possible to share named files with others using the same cloud computing account or environment. It is also possible to use these buckets as static content stores for assets you'd like to make available via the web or in other ways. There are command line utilities for each cloud provider which can allow you to add, sync and remove files from bucket stores. In general you shuld not think of buckets as a "live filesystem" that any software on your virtual machine can read and write to. This is a design trade-off made by bucket storage architectures to focus on the GET/PUT of whole files rather than the random access allowed by traditional filesystems. 


##### Block Storage 
 
| Platform | Name |
| -------- | -------- |
| AWS     | EBS |
| Google | Persistent Disks |
| Microsoft | Disks | 

Block storage is online storage that can be "mounted" to the running virtual machine, block storage systems are priced based on the performance level you can expect under peak loads. Because these block storage systems are shared across many customers, paying more for the storage will ensure that you have an ammount of I/O (Input/Output) reserved when you might need it. If you're building a virtual machine that will be running a database (PostgreSQL, MySQL, MongoDB etc.) you'll want to carefully consider the cost/benefit trade off of the increased cost for higher I/O. These block storage "disks" can be mounted, snapshotted and copied in various ways on the back end cloud management interfaces. The ways in which these are treated varies, but overall there in the cloud management layer, disks with a given identifier (disk-ID) can be mounted to a running virtual machine identifier (VM-ID). There is a manual step in all of these environments of creating and formatting the filesystem as you see fit for your needs. Linux has a range of different filesystems that can be used, the ext{2,3,4} family of filesystems, XFS and others. Depending on your needs or your experience, it is up to you the Virtual Machine manager to select a filesytem type for your block devices that best fits your needs. Many tuturoials show the usage of `ext4`, which is a perfectly reasonable choice. 

###### Hosted Databases 

Hosted databases allow you to reduce the management costs of having an IT adminsitrator who manages and patches your database server and underlying database software. These are usually priced at a small price premium over the cost of the underlying VM if you were to manage the DB yourself. Things like backups, access controls, encryption of data and columns are all managed by the database management system of the cloud provider. Cloud providers will often offer a preferred solution which may be an open source database like PostgreSQL modified for their cloud environment in ways that make it easier to manage for them. They'll also allow you to run many open source database options (including PostgreSQL and MySQL depending on the cloud environment). You'll find that each cloud platform will market their own DB solution and one or more open source DB solutions as well. From PostgreSQL to MongoDB or even Oracle, you can spend a lot or a little to build a hosted databases solution in the cloud. 

| Platform | Name |
| -------- | -------- |
| AWS     | RDS - Relational Database Service |
| Google | Cloud SQL |
| Microsoft | Azure Database | 

**Advantages:**

* Don't have to worry about operating system or database patches
* Backups and recovery can be integrated
* Tuning and performance are handled by the provider

**Disadvantages:**

*
* 
*   You're a step removed from being able to configure and optimize the database for unique use cases.
* Costs can be higher, but factor in system management/IT time when considering. 
* Storage costs can be expensive up-front when the DB is new/empty, start small and understand how your chosen platform scales. 


## Onshore 

###  Catalyst IT 

The Catalyst Cloud is an on-shore NZ [OpenStack](https://www.openstack.org/) offering which enables you to run Virtual Machines, store data in bucket or block storage systems. It is generally equivalent to other major cloud providers. Being that it is NZ-focused it does not realize the economies of scale of other large cloud providers, but is able to meet regulatory requirements where data cannot leave NZ. 

* [Discounts](https://catalystcloud.nz/pricing/discounts/) 


## Offshore

### Major players

There are three major cloud platforms, Amazon (Amazon Web Services), Google (Google Cloud) and Microsoft (Azure). These three providers represent the largest and most comprehensive cloud environments available today. With this competitiveness comes a certain amount of complexity in their use. There are entire careers built around managing companies resources in one or more cloud environments. That being said, researchers who develop some skills and understanding of these cloud platforms can benefit from having computational resources at the ready if they're willing to pay for it. 

#### Amazon Web Services

* [Grants for Research](https://aws.amazon.com/grants/)

#### Microsoft Azure 

Microsoft has three AI-focuesed topical thrust areas for grants:

* [AI for Humanitarian Action](https://www.microsoft.com/en-us/ai/ai-for-humanitarian-action)
* [AI for Earth](https://www.microsoft.com/en-us/aiforearth)
* [AI for Accessibility](https://www.microsoft.com/en-us/ai-for-accessibility)


#### Google Cloud

The Google Cloud is a general purpose cloud environment with a mix of IaaS and PaaS offereings. It is the 

* [Grants for Research](https://lp.google-mkto.com/gcp-research-credits-FAQ.html)

## Cloud Billing 

Integrating your internal organisational accounting to Cloud accounting


### Minor Players 

The minor players in the cloud space tend to focus on certain areas of 

#### Digital Ocean

Digital ocean speciallises in packaged VMs where the monthly cost is manageable and known ahead of time. For as little as $5/month a VM can be run at Digital Ocean that hosts services at a static IP address in the cloud. Getting started with running your first VM at a place like Digital Ocean is quite a bit simpler than using one of the three major providers. 

#### Vultr 

The [Cloud Compute](https://www.vultr.com/products/cloud-compute/) product at [Vultr](https://vultr.com) is A low-cost and very simple cloud provider.  With VMs starting at US $2.50 per month, and data centers around the world, Vultr is a simple and low cost solution for Virtual Machines very similar to Digital Ocean. 

#### RStudio cloud


### What about HPC 

New Zealand is fortunate enough to have NeSI - The New Zealand eScience Infrastrucutre, a collaboration of academic institutions, CRIs and govt to bring a world class HPC environment to NZ. While this document focuses largely on cloud environments, NeSI provides a research platform that can potentially be used in your research for little or no direct cost to your project. It is worth exploring how NeSI's tools, platforms and services might be a better fit for your research application. If you know you'll need huge amounts of shared memory and multi-core computational capabilities, it is most likely worth using the NeSI platform to support your research. 

### Research Network Considerations 

REANNZ stuff goes here. 

* Peering where, when how 
* End to end care of data
* International community 
* tuning & performance - perfsonar - Fiona Nodes  
* GLOBUS 

### Data sovereignty and governance

Data sovereignty is becoming a decision making factor on where data is stored. Currently discussions are underway in regard to [Maori data sovereignty and data governance](https://static1.squarespace.com/static/58e9b10f9de4bb8d1fb5ebbc/t/5bda208b4ae237cd89ee16e9/1541021836126/TMR+Māori+Data+Sovereignty+Principles+Oct+2018.pdf).

### Advanced Topics 

#### Serverless Compute 

The term "serverless" is a pieces of jargon used to describe ways in which full computational environments can be run in an as-needed way. The root technology of this is OS-level containerization (Docker/Singularity) which enables specifying an entire compute environment. Then based on the workload expected in that environment, the containers can be run on special virtual machines which you have little host system-level control over. We recommend that you explore and use a virtual machine environment and become familiar with it before you take the leap to serverless. Serverless has some benefits to scale-out and pricing for services you don't want to have running at all times, but that benefit comes at an increased level of concern you'll need to have for how individual services and components interact with each other. 

| Platform | Name |
| -------- | -------- |
| AWS     | Lambda  |
| Google | Cloud Functions  |
| Microsoft | Azure Functions | 

## Glossary

S3
: Simple Storage System - Amazon's GET/PUT bucket storage system.

Glacier
: Amazon's low-cost cold-storage tier. 

EC2
: Elastic Compute Cloud - Amazon's virtual machine service

SaaS
: Software as a Service

PaaS
: Platform as a service

IaaS 
: Infrastructure as a service 

distribution
: A combinatoin of the linux kernel and associated programs which run in it. These distributions have different package managers and design styles. Popular distributions include Ubuntu, Debian, RedHat and CentOS. 

