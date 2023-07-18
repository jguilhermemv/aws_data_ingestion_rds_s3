# AWS Data Ingestion Pipeline Project

This project employs an AWS CloudFormation template to build a data ingestion pipeline on AWS. The pipeline uses Amazon RDS as the source, AWS Data Migration Service (DMS) as the data ingestion tool, and Amazon S3 as the destination.

## Overview
The CloudFormation script in this repository is devised to establish a data ingestion infrastructure. It provisions a PostgreSQL database on Amazon RDS, manages the database credentials with AWS Secrets Manager, assigns necessary permissions using IAM, and configures an AWS DMS instance to orchestrate the data transfer from the RDS instance to an S3 bucket.

## Resources
The following AWS resources are provisioned by this CloudFormation script:

1. **DBSecret**: This is an AWS Secrets Manager secret that securely holds the credentials for the PostgreSQL database. 

2. **DMSRole**: An IAM role that grants the Data Migration Service access permissions to the S3 bucket.

3. **MyDB**: This is a PostgreSQL database instance hosted on Amazon RDS. The database credentials are securely stored in the `DBSecret` Secrets Manager secret.

4. **MyBucket**: An Amazon S3 bucket designed for the storage of data ingested from the RDS database. The bucket name includes the account ID and stack name for uniqueness.

5. **DMSInstance**: This is an AWS Data Migration Service instance used to handle the data migration from the RDS database to the S3 bucket.

6. **SourceEndpoint**: This refers to the endpoint of the PostgreSQL source database for the DMS instance.

7. **DMSTask**: This is a task created for the DMS instance, designed to facilitate the full-load migration of data from the source PostgreSQL database to the target S3 bucket.

## Prerequisites
Before you can use this CloudFormation script, ensure you have the following:

- An AWS account.
- AWS CLI installed and correctly configured on your system.

## Deployment

To deploy the pipeline infrastructure, execute the following AWS CLI command:

```bash
aws cloudformation deploy --template-file aws_data_ingestion_infrastructure.yml --stack-name rds-dms-s3
```

Replace `<your-stack-name>` with your preferred name for the CloudFormation stack.

## Cleanup

To delete the provisioned resources when they are no longer required, run the following AWS CLI command:

