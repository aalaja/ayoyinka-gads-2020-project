# LAB: Getting Started with Cloud Storage and Cloud SQL
## Objectives:

In this lab, you learn how to perform the following tasks:
* Create a Cloud Storage bucket and place an image into it.
* Create a Cloud SQL instance and configure it.
* Connect to the Cloud SQL instance from a web server.
* Use the image in the Cloud Storage bucket on a web page.


## Steps:

**1. Deploy a web server VM instance:**

```
gcloud config set project $DEVSHELL_PROJECT_ID

gcloud config set compute/zone us-central1-a

gcloud compute instances create bloghost --machine-type=e2-medium --image-project=debian-cloud --image=debian-9-stretch-v20200805 --metadata startup-script="apt-get update; apt-get install apache2 php php-mysql -y; service apache2 restart" --subnet=default --tags=http-server

gcloud compute firewall-rules create default-allow-http --action=ALLOW --direction=INGRESS --network=default --priority=1000 --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
```

**2. Create a Cloud Storage bucket using the gsutil command line:**

  a. Select your chosen location for the bucket. It is advisable to store your bucket close to your VMs

  ```
  export LOCATION=US
  ```

  b.  Create a bucket named after your project ID as buckets have to be globally unique and project IDs are globally unique as well.

  ```
  gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID
  ```

  c. Copy a file into your environment.
  ```
  gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png
  ```

  d. Copy the file into your bucket.
  ```
  gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
  ```

  e. Modify the Access Control List of the file in the bucket to make it readable to all.

  ```
  gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
  ```

  **3. Create the Cloud SQL instance:**

  ```
  gcloud sql instances create blog-db --root-password=admin --database-version=MYSQL_5_7 --zone=us-central1-a

  gcloud sql users create blogdbuser --instance=blog-db --host='%' --password=blogdbuserp
  ```
b. Check for the external IP address of the `bloghost` VM instance.
  ```
  gcloud compute instances list
  ```
c. Add and authorize the network. Ensure the IP address is followed by/32.
  ```
  gcloud sql instances patch blog-db --authorized-networks=34.123.84.17/32
  ```

**4. Configure an application in a Compute Engine instance to use Cloud SQL:**

  a. SSH into the `bloghost` VM instance.
  ```
  gcloud compute ssh bloghost --zone=us-central1-a
  cd /var/www/html
  sudo nano index.php
  ```
  b. Edit the file by copying, pasting and saving below code.
  ```
  <html>
<head><title>Welcome to my excellent blog</title></head>
<body>
<h1>Welcome to my excellent blog</h1>
<?php
 $dbserver = "35.188.79.29";
$dbuser = "blogdbuser";
$dbpassword = "blogdbuserp";
// In a production blog, we would not store the MySQL
// password in the document root. Instead, we would store it in a
// configuration file elsewhere on the web server VM instance.

$conn = new mysqli($dbserver, $dbuser, $dbpassword);

if (mysqli_connect_error()) {
        echo ("Database connection failed: " . mysqli_connect_error());
} else {
        echo ("Database connection succeeded.");
}
?>
</body></html>
```
c. Restart the web server.
```
sudo service apache2 restart
```

**5. Configure an application in a Compute Engine instance to use a Cloud Storage object:**

a. SSH into the `bloghost` VM instance.
```
gcloud compute ssh bloghost --zone=us-central1-a
cd /var/www/html
sudo nano index.php
```
b. Edit the file by copying, pasting and saving below code. Note that the image source is the public URL of the image copied earlier into our Storage Bucket. Note that the bucket URL is in the format:
`https://storage.googleapis.com/<project-id>/<storage-file-name>`


```
<html>
<head><title>Welcome to my excellent blog</title></head>
<body>
<h1>Welcome to my excellent blog</h1>
<img src='https://storage.googleapis.com/qwiklabs-gcp-04-9a4a6c6531a3/my-excellent-blog.png'>
<?php
$dbserver = "35.188.79.29";
$dbuser = "blogdbuser";
$dbpassword = "blogdbuserp";
// In a production blog, we would not store the MySQL
// password in the document root. Instead, we would store it in a
// configuration file elsewhere on the web server VM instance.

$conn = new mysqli($dbserver, $dbuser, $dbpassword);

if (mysqli_connect_error()) {
      echo ("Database connection failed: " . mysqli_connect_error());
} else {
      echo ("Database connection succeeded.");
}
?>
</body></html>
```

c. Restart the web server.
```
sudo service apache2 restart
```
