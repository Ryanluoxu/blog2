---
title: Database - PostgreSQL on AWS
date: 2017-12-16 11:00:28
categories: Database
tags: [database, Postgres, install, setup, AWS]
---
![SQuirreL SQL Client](/images/SQuirreL-SQL-Client.png)
- Create Postgres database on Amazon Web Service. 
- Install pgAdmin as client application to connect to database.
- Install SQuirreL SQL Client to connect to database.

I personally prefer to use SQuirreL SQL Client than pgAdmin.

<!--more-->

# Create Postgres Database on AWS

Reference: [Create and Connect to a PostgreSQL Database](https://aws.amazon.com/getting-started/tutorials/create-connect-postgresql-db/)

1. create an AWS account before creating database.
2. go to this page: [AWS services](https://ap-southeast-1.console.aws.amazon.com/console/home?region=ap-southeast-1)
3. search for `RDS` and click
4. search for `Create instance` and click `Launch a DB instance`

Below steps made under `Free Usage Tier`
1. Engine: `PostgreSQL`
2. Use case: `Dev/Test`
3. DB details: 
	- tick `Only enable options eligible for RDS Free Usage Tier`
	- DB instance identifier: `myPostgresDB`
	- Master username: `xxxxxx`
	- Master password: `xxxxxx`
4. Configure advanced settings
	- tick `Yes` for Public accessibility
	- Database name: `Demo`

Click `Launch DB instance` to create.

# Use pgAdmin as Client Application

Reference: [Connecting to a DB Instance Running the PostgreSQL Database Engine](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToPostgreSQLInstance.html)

To connect to database, go to `RDS`>`Instances`>`postgresql` and collect below info:
- Endpoint
- Port
- Username
- Password

1. Go to [Download - pgAdmin](https://www.pgadmin.org/download/) to download `pgAdmin 4 v2.0`
2. Install and Open pgAdmin
3. Right click `Servers` - `Create` - `Server...`
4. General:
	- Name: `Demo`
	- Comments: `Demo`
5. Connection: 
	- Host: `<Endpoint>`
	- Port:	`<Port>`
	- Username: `<Username>`
	- Password: `<Password>`
	- Role: Empty

Click `Save`


Done..


# Use Squirrel SQL Client as Client Application

1. Install Squirrel SQL Client Application
	- Go to [SQuirreL SQL Client Download](http://squirrel-sql.sourceforge.net/#installation)
	- Download and install
2. Download PostgreSQL JDBC Driver
	- Go to [PostgreSQL JDBC Download](https://jdbc.postgresql.org/download.html)
	- Choose `PostgreSQL JDBC 4.2 Driver, 42.1.4`
	- Get `postgresql-42.1.4 driver`
3. Add PostgreSQL driver for Squirrel SQL Client
	- Open `Squirrel SQL Client`
	- Choose `Driver` on the left
	- Choose `+` to add driver
	- Choose `Extra Class Path` and `Add`
	- Choose `postgresql-42.1.4 driver` just downloaded.
	- Fill up:
		- PostgreSQL
		- jdbc:postgresql://<host>:<5432>/<database>
		- http://jdbc.postgresql.org
		- org.postgresql.Driver
	- OK
4. Add Alias
	- Choose `Aliases` on the left
	- Choose `+` to add
	- Choose blue tick `PostgreSQL` as Driver
	- Fill up:
		- Demo
		- jdbc:postgresql://postgresql.cfqvncjculpj.ap-southeast-1.rds.amazonaws.com:5432/Demo
		- userName
		- password
	- `Test`
	- `Connect`
	- `OK`


Done..