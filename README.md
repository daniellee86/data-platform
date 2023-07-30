# Data Platform 🚀

![Python](https://img.shields.io/badge/Python-3.9-blue?logo=python&style=flat)
![AWS](https://img.shields.io/badge/AWS-Cloud-orange?logo=amazon-aws&style=flat)
![SQL](https://img.shields.io/badge/SQL-PostgreSQL-blue?logo=postgresql&style=flat)
![Data Modeling](https://img.shields.io/badge/Data%20Modeling-Star%20Schema-blueviolet?style=flat)
![ETL](https://img.shields.io/badge/ETL-Extract%20Transform%20Load-green?style=flat)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Manipulation-yellow?logo=pandas&style=flat)
![CSV](https://img.shields.io/badge/CSV-Data%20Storage-informational?style=flat)
![Parquet](https://img.shields.io/badge/Parquet-Columnar%20Storage-yellowgreen?style=flat)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-CI%2FCD-brightgreen?logo=github-actions&style=flat)
![CI/CD](https://img.shields.io/badge/CI/CD-Automation-yellow?style=flat)
![Terraform](https://img.shields.io/badge/Terraform-Infrastructure%20as%20Code-lightgreen?logo=terraform&style=flat)
![Makefile](https://img.shields.io/badge/Makefile-Streamlines%20Operations-brightgreen?style=flat)


## Objective
Create a data platform; a collection of cloud based Python applications that extract data from an OLTP database (and potentially other sources), archive it in a data lake, and make it availabale in a remodelled OLAP data warehouse.


## Architecture: 🏛️
The pipeline's robust architecture comprises the following key components:

📊 Data Sources: The data originates from a Postgres database named ToteSys, simulating a real-world operational data source.

🗃️ AWS S3 Buckets: Ingested data is efficiently stored in an S3 bucket in CSV format. Meanwhile, the transformed star schema files reside in a separate S3 bucket in Parquet format. 

🐍 AWS Lambda Functions: Automation drives the pipeline through three dedicated Lambda functions, each serving a unique purpose. These include the ingestion function, the transformation function, and the warehouse loader function.

⏰ Triggers: AWS EventBridge and Lambda Triggers manage the pipeline's flow, ensuring timely execution of each Lambda function.

🌟 Layers: A Lambda layer enriches the environment with the Pandas library, enhancing data manipulation capabilities where needed.

🔑 IAM Roles and Policies: Robust IAM roles and policies control access to AWS resources and services, ensuring security and data integrity.

🏛️ Data Warehouse: A Postgres database serves as the data warehouse, efficiently organizing and storing the transformed data in a STAR schema.


## Installation: ⚙️
ensure you have the following :

☁️ An AWS Account for deployment and interaction with AWS services.

🐍 Python 3.9 installed for running the project's Python applications.

🛠️ AWS CLI installed to interact with AWS resources from the command line.

⚙️ Required Libraries: PG8000 and Pandas.

📦 Terraform to provision AWS resources - infrastructure as code.


# Usage: 🎯
follow these steps:

📥 Clone the repository.

⚙️ Configure AWS CLI with your AWS account credentials.

📦 Prepare Python files for each Lambda function, ensuring that necessary libraries are zipped separately. The Pandas library is configured as a Lambda layer in AWS.

🛠️ The project includes a Makefile to simplify setup and operations. Key commands include:

-  make or make create-environment: Creates a virtual environment with Python 3.9 and installs dependencies from the requirements.txt file.
-  make run-checks: Executes security tests (using safety and bandit), runs flake8 for compliance, and performs pytest unit tests for each Lambda function.

🏗️ Change to the Terraform directory and execute the following commands:
```bash
terraform init
terraform plan
terraform apply
```

## The Data

### OLTP
The primary data source for the project is a moderately complex database called `totesys` which is meant to simulate the back end data of a commercial application. Data is inserted and updated into this database several times a day. (The data itself is entirely fake and meaningless, as a brief inspection will confirm.)

The full ERD for the OLTP database is detailed [here](https://dbdiagram.io/d/6332fecf7b3d2034ffcaaa92).

The tables ingested from `totesys` are:
|tablename|
|----------|
|counterparty|
|currency|
|department|
|design|
|staff|
|sales_order|
|address|
|payment|
|purchase_order|
|payment_type|
|transaction|


### OLAP
The ERD for the OLAP data warehouse star schema:
 - ["Sales" schema](https://dbdiagram.io/d/637a423fc9abfc611173f637)

The overall structure of the resulting data warehouse is shown [here](https://dbdiagram.io/d/63a19c5399cb1f3b55a27eca).

The list of populated tables in the complete warehouse is:

|tablename|
|---------|
|fact_sales_order|
|dim_staff|
|dim_location|
|dim_design|
|dim_date|
|dim_currency|
|dim_counterparty|


## Runtime: Uninterrupted Data Flow ⏱️
The data ingestion Lambda runs every 3 minutes, efficiently storing updated or changed tables in date and time stamped folders. The address and department tables undergo continuous ingestion due to their dependencies in the Star Schema, stored in their respective directories.

The transformation Lambda triggers when changes occur in the ingestion bucket. The warehouse loader Lambda executes upon changes to the transformation bucket.

## Credentials: Securing Access 🔒
To ensure secure access to the data sources and the data warehouse, AWS Secrets Manager is employed. Credentials for the ToteSys database (hostname, database name, username, and password) are securely stored and retrieved using the db_connection.py file. Similarly, the warehouse database credentials are also managed via AWS Secrets Manager.

## Conclusion: Unleashing Data's Full Potential 🌟
With this Platform / Data Pipeline I established a scalable, fault-tolerant, and easily maintainable solution. Leveraging Github actions and Terraform, I automated the deployment of the pipeline's cloud-based infrastructure.


