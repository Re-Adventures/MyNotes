# Google Cloud

* For downloading compliance information, we can go to [here](http://cloud.google.com/security/compliance).


* There are four ways to interact with GCP(Google Cloud Platform):
1. Cloud Platform Console (Web interface)
2. Cloud shell & sdk (CLI)
3. Cloud console mobile app (ios & Android)
4. REST API


* We can use the **API Explorer** tool that lets us try & test the APIs using browser


For controlling GCP resources, we can use the client libraries:
1. Cloud client libraries (recommended)
2. Google API client library (open source)


## VPC networks

* VPC (Virtual Private Cloud) networks connect your Google Cloud Platform resources to each other and to the internet. They belong to GCP Projects

* Google Cloud VPC networks are global; subnets are regional
* Subnets can span the zones that make up a region
* You can also have resources in different zones on the same subnet

* VPC have pre-built routing tables, we don't need to get a router.

* VPC also have firewall already enabled, which we can configure using metadata tags on the Compute Engine instances.

* For example, we can tag all the web server to be "web" & can apply to rule like port 80 & 443 are allowed


## Compute Engine

* Compute Engine lets us run virtual machines on google cloud.
* We can create virtual machines using GCP console or gcloud command line tool.

* Properties:
  ● High CPU, high memory, standard and shared-core machine types
  ● Persistent disks
    ○ Standard, SSD, local SSD
    ○ Snapshots
  ● Resize disks with no downtime
  ● Instance metadata and startup scripts

* We can also define a startup script, for example installing some packages when the machine is created

* For each VM that you run for more than 25% of a month, Compute Engine automatically gives you a discount for every incremental minute. You can get up to a 30% net discount for VMs that run the entire month.

### Pre-emptable VM

* A pre-emptable VM is different from ordinary Compute Engine VM in only one perspect, which is we can give Compute Engine permissions to terminate/pause some task if the resources are needed else where


### Committed use discounts

* If your workload is stable and predictable, you can purchase a specific amount of vCPUs and memory for up to a 57% discount off of normal prices in return for committing to a usage term of 1 year or 3 years


## Cloud Load Balancing

* Using cloud load balancing a single IP is acts as a front-end to all our backend instances around the globe




