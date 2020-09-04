# LAB: Creating Virtual Machines
## Objectives:

In this lab, you learn how to perform the following tasks:
* Create several standard VMs
* Create advanced VMs

## Steps:

**1. Create a utility virtual machine:**

```
gcloud config set project $DEVSHELL_PROJECT_ID

gcloud compute instances create instance-1 --zone=us-central1-c --machine-type=n1-standard-1 --image-project=debian-cloud --image=debian-9-stretch-v20200902 --no-address --subnet=default
```

**2. Create a Windows virtual machine:**

```
gcloud compute instances create windows-vm --zone=europe-west2-a --machine-type=n1-standard-2 --image-project=windows-cloud --image=windows-server-2016-dc-core-v20200813 --boot-disk-type=pd-ssd --boot-disk-size=100GB --tags=http-server,https-server

gcloud compute firewall-rules create default-allow-http --action=ALLOW --direction=INGRESS --network=default --priority=1000 --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server

gcloud compute firewall-rules create default-allow-https --action=ALLOW --direction=INGRESS --network=default --priority=1000 --rules=tcp:443 --source-ranges=0.0.0.0/0 --target-tags=https-server
```

> Set the password for the VM

```
gcloud compute reset-windows-password windows-vm --zone=europe-west2-a
```

**3. Create a custom virtual machine:**

```
gcloud compute instances create custom-vm --zone=us-west1-b --machine-type=custom-6-32768 --subnet=default --image-project=debian-cloud --image=debian-9-stretch-v20200902
```
> Connect to the custom VM via SSH

```
gcloud compute ssh custom-vm --zone=us-west1-b
```
>Enter y when prompted. For the password, hit `Enter` when prompted.

> View memory info and swap space on the custom VM

```
free
```

>View details about the RAM installed on the VM

```
sudo dmidecode -t 17
```

>View the number of processors

```
nproc
```

>View details about the CPUs installed on the VM and exit

```
lscpu
exit
```
