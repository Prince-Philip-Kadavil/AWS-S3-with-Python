# AWS-S3-with-Python
Backing Up Configuration Files to AWS S3 with Python

Data backup is a crucial aspect of any system administration in today's digital world. It ensures that your data is safe and can be restored in case of any unforeseen circumstances. This post will discuss a Python script that backs up all the configuration files from a server to an Amazon S3 bucket.

## The Python Script

The script we are discussing is written in Python and uses the `os`, `posixpath`, `tarfile`, and `boto3` libraries. The script performs two main tasks:

1. It creates a tar file (`config.tar`) containing all the `.conf` files from the `/etc/httpd/` directory.
2. It uploads the created tar file to an Amazon S3 bucket.

Let's break down the script to understand it better.

## Step 1: Creating the 'config.tar' File

The first part of the script uses the `os` and `tarfile` libraries to create a tar file containing all the `.conf` files. The `os.walk()` function is used to traverse through the `/etc/httpd/` directory and its subdirectories. For each file in these directories, the script checks if the file ends with `.conf` (indicating it's a configuration file). If it is, the file is added to the tar file.

```python
with tarfile.open('/tmp/config.tar', 'w') as tar:
  for item in os.walk('/etc/httpd/'):
    curDir, subDir, files = item
    for file in files:
      abspath = posixpath.join(curDir, file)
      if abspath.endswith('.conf'):
        tar.add(abspath)
```

## Step 2: Uploading the 'config.tar' File to an S3 Bucket

The second part of the script uses the `boto3` library to upload the created tar file to an Amazon S3 bucket. The `boto3.resource('s3')` function is used to create a resource service client for Amazon S3. 

The script first checks if the specified bucket exists. The script creates a new bucket with the specified name if it doesn't. Then, the script uploads the tar file to the bucket.

```python
# Step 2: Upload the 'config.tar' file to an S3 bucket
bucket_name = 'cloud-crafter-config-backup'  # Replace with your S3 bucket name
current_datetime = datetime.now().strftime("%Y-%m-%d-%H-%M-%S")
file_name = 'config-backup-{}.tar'.format(current_datetime)                    


s3 = boto3.resource('s3')

# Check if the bucket already exists
bucket = s3.Bucket(bucket_name)
if not bucket.creation_date:
    # Bucket doesn't exist, create it
  s3.create_bucket(Bucket = bucket_name)

# Upload the file to the bucket
s3.Bucket(bucket_name).upload_file('/tmp/config.tar', file_name)
```

## Conclusion

This script provides a simple and efficient way to back up configuration files to an Amazon S3 bucket. It can be easily modified to back up different types of files or to back up files to different locations. It's a great starting point for anyone looking to automate their backup processes with Python and AWS.
