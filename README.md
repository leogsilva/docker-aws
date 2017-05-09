# What is docker-aws

[![Circle CI](https://circleci.com/gh/cgswong/docker-aws/tree/master.svg?style=svg)](https://circleci.com/gh/cgswong/docker-aws/tree/master)

[Docker](https://docker.io) image for running the [Amazon Web Services Command Line Interface](http://aws.amazon.com/cli/) and [s3cmd](https://github.com/s3tools/s3cmd). It creates a [Docker](https://docker.io) image containing all dependencies needed to run `aws` and `s3cmd`. That way, you can run these tools in a [Docker](https://docker.io) container without setting the dependencies on the host system.


## How to use
There are specific CLI tools within the full range of the AWS CLI tool chain. Each of these has been mapped to a branch and tag so if you only need a specific CLI tool and/or have image size concerns there is something here to meet your needs. If you want all the AWS tool goodies, i.e. AWS CLI, RDS CLI, and S3CMD, then select the `master` branch with the `latest` tag in [Docker Hub](https://registry.hub.docker.com/u/cgswong/aws). Below are the mappings and usages:

| GitHub Branch | Docker Tag | Usage Example                       |
| ------------- | ---------- | ----------------------------------- |
| master        | latest     | `docker run -it cgswong/aws:latest` |
| aws           | aws        | `docker run -it cgswong/aws:aws`    |
| s3cmd         | s3cmd      | `docker run -it cgswong/aws:s3cmd`  |
| rds           | rds        | `docker run -it cgswong/aws:rds`    |

The master/latest starts a `bash` shell so you can use whatever tool you need. For the other containers they will start with the `--version` flag which shows the version for the respective CLI tool. Simply specify whatever options you need for the respective tool (this will replace the default `--version` flag).

**Note:**
- You can check the `release.md` file in the repository as to what version of the AWS CLI is being used.
- AWS has deprecated the RDS CLI and as such I am no longer maintaining that branch. The main AWS CLI should now be used.


## Build from Source
1. Make sure [Docker](https://www.docker.com) is installed.

2. Clone _docker-aws_ from [GitHub](https://github.com/cgswong/docker-aws)

   ```sh
   git clone https://github.com/cgswong/docker-aws.git
   ```

3. Build the docker image

   ```sh
   cd docker-aws
   docker build -t [your_name]/aws:latest .
   ```

4. Run a docker container with that image

   ```sh
   docker run -it [your_name]/aws:latest
   ```

## AWS Credentials
There are three primary methods for setting up your AWS credentials:

1. Run the setup using the container, i.e. when you run the CLI tools for the first time, you need to configure the [aws cli](http://aws.amazon.com/cli) with your AWS credentials. For example:


   ```sh
   / # aws configure
   AWS Access Key ID [None]: [YOUR_AWS_ACCESS_KEY_ID_HERE]
   AWS Secret Access Key [None]: [YOUR_AWS_SECRET_ACCESS_KEY_ID_HERE]
   Default region name [None]: [YOUR_AWS_REGION_HERE]
   Default output format [None]: [YOUR_AWS_OUTPUT_FORMAT_HERE]
   ```

2. The volume `/root/.aws` has been exposed within the image for external access. Therefore you can mount the AWS credential file to the container:

   ```sh
   docker run --volume ~/.aws:/root/.aws cgswong/aws:latest aws [some_command]
   ```

3. By configuring your EC2 instances to make use of IAM Roles, the AWS CLI tools can then transparently make use of this role. Note however, that not all CLI tools can currently leverage this functionality, namely some of the RDS tools.

## Getting started with the AWS CLI and s3tools
For documentation on the AWS CLI, see the [AWS command line interface documentation](http://aws.amazon.com/documentation/cli/) and the [aws-cli GitHub page](https://github.com/aws/aws-cli). For S3 tools, see the [S3 tools documentation](http://s3tools.org/usage).

dynamodump
==========

[![Buildstatus](https://travis-ci.org/bchew/dynamodump.svg)](https://travis-ci.org/bchew/dynamodump)		

Simple backup and restore script for Amazon DynamoDB using boto to work similarly to mysqldump.

Suitable for DynamoDB usages of smaller data volume which do not warrant the usage of AWS Data Pipeline for backup/restores/empty.

dynamodump supports local DynamoDB instances as well (tested with [dynalite](https://github.com/mhart/dynalite)).

Usage
-----
```
usage: dynamodump.py [-h] [-m MODE] [-r REGION] [--host HOST] [--port PORT]
                     [--accessKey ACCESSKEY] [--secretKey SECRETKEY]
                     [-p PROFILE] [-s SRCTABLE] [-d DESTTABLE]
                     [--prefixSeparator PREFIXSEPARATOR] [--noSeparator]
                     [--readCapacity READCAPACITY]
                     [--writeCapacity WRITECAPACITY] [--schemaOnly]
                     [--dataOnly] [--skipThroughputUpdate]
                     [--dumpPath DUMPPATH] [--log LOG]

Simple DynamoDB backup/restore/empty.

optional arguments:
  -h, --help            show this help message and exit
  -m MODE, --mode MODE  'backup' or 'restore' or 'empty'
  -r REGION, --region REGION
                        AWS region to use, e.g. 'us-west-1'. Use 'local' for
                        local DynamoDB testing
  --host HOST           Host of local DynamoDB [required only for local]
  --port PORT           Port of local DynamoDB [required only for local]
  --accessKey ACCESSKEY
                        Access key of local DynamoDB [required only for local]
  --secretKey SECRETKEY
                        Secret key of local DynamoDB [required only for local]
  -p PROFILE, --profile PROFILE
                        AWS credentials file profile to use. Allows you to use
                        a profile instead of accessKey, secretKey
                        authentication
  -s SRCTABLE, --srcTable SRCTABLE
                        Source DynamoDB table name to backup or restore from,
                        use 'tablename*' for wildcard prefix selection or '*'
                        for all tables
  -d DESTTABLE, --destTable DESTTABLE
                        Destination DynamoDB table name to backup or restore
                        to, use 'tablename*' for wildcard prefix selection
                        (defaults to use '-' separator) [optional, defaults to
                        source]
  --prefixSeparator PREFIXSEPARATOR
                        Specify a different prefix separator, e.g. '.'
                        [optional]
  --noSeparator         Overrides the use of a prefix separator for backup
                        wildcard searches [optional]
  --readCapacity READCAPACITY
                        Change the temp read capacity of the DynamoDB table to
                        backup from [optional]
  --writeCapacity WRITECAPACITY
                        Change the temp write capacity of the DynamoDB table
                        to restore to [defaults to 25, optional]
  --schemaOnly          Backup or restore the schema only. Do not
                        backup/restore data. Can be used with both backup and
                        restore modes. Cannot be used with the --dataOnly
                        [optional]
  --dataOnly            Restore data only. Do not delete/recreate schema
                        [optional for restore]
  --skipThroughputUpdate
                        Skip updating throughput values across tables
                        [optional]
  --dumpPath DUMPPATH   Directory to place and search for DynamoDB table
                        backups (defaults to use 'dump') [optional]
  --log LOG             Logging level - DEBUG|INFO|WARNING|ERROR|CRITICAL
                        [optional]
```

Backup files are stored in a 'dump' subdirectory, and are restored from there as well by default.

AWS example
-----------
The following examples assume your AWS access key and secret key is present in ~/.boto

Single table backup/restore:
```
python dynamodump.py -m backup -r us-west-1 -s testTable

python dynamodump.py -m restore -r us-west-1 -s testTable
```
Multiple table backup/restore (assumes prefix of 'production-' of table names, use --prefixSeparator to specify a
different separator):
```
python dynamodump.py -m backup -r us-west-1 -s production*

python dynamodump.py -m restore -r us-west-1 -s production*
```
The above, but between different environments (e.g. production-* tables to development-* tables):
```
python dynamodump.py -m backup -r us-west-1 -s production*

python dynamodump.py -m restore -r us-west-1 -s production* -d development*
```
Backup all tables and restore only data (will not delete and recreate schema):
```
python dynamodump.py -m backup -r us-west-1 -s "*"

python dynamodump.py -m restore -r us-west-1 -s "*" --dataOnly
```
Dump all table schemas and create the schemas (e.g. creating blank tables in a different AWS account):
```
python dynamodump.py -m backup -r us-west-1 -p source_credentials -s "*" --schemaOnly

python dynamodump.py -m restore -r us-west-1 -p destination_credentials -s "*" --schemaOnly
```

Local example
-------------
The following assume your local DynamoDB is running on localhost:4567 and is accessible via 'a' as access/secret keys.
```
python dynamodump.py -m backup -r local -s testTable --host localhost --port 4567 --accessKey a --secretKey a

python dynamodump.py -m restore -r local -s testTable --host localhost --port 4567 --accessKey a --secretKey a
```
Multiple table backup/restore as stated in the AWS examples are also available for local.
