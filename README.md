moniDBase
A repository containing SQL schema and initialization scripts for the "moniDB" database, which stores user and website scanning information.
Overview
This repository contains the necessary files to set up a PostgreSQL database for a website scanning application. The database structure includes:

users table: Stores user information including authentication details
scans table: Stores website scan results with SSL and status information

Repository Contents

init_db.sql: SQL initialization script that creates the necessary tables and constraints
install_db.sh: Shell script for easy one-line installation
Additional application files (if any)

Requirements

Amazon EC2 instance (Ubuntu recommended)
Docker
Git

Quick Installation
The easiest way to install the database is using our installation script:
bashCopycurl -s https://raw.githubusercontent.com/danette14/moniDBase/main/install_db.sh | bash
This will automatically install Docker, set up PostgreSQL, and initialize the database with the required schema.
Manual Installation Guide
Alternatively, follow these steps to manually set up the moniDB database on an EC2 instance:
1. Connect to your EC2 instance
bashCopyssh -i your-key.pem ubuntu@your-ec2-ip
2. Update system and install Docker
bashCopysudo -i
apt update -y && apt upgrade -y
apt install docker.io -y
3. Create a directory for PostgreSQL data persistence
bashCopymkdir -p ~/postgres_data
sudo chown -R 999:999 ~/postgres_data
4. Run PostgreSQL in a Docker container
bashCopydocker run -d \
  --name my_postgres_db \
  -e POSTGRES_USER=myuser \
  -e POSTGRES_PASSWORD=mypassword \
  -e POSTGRES_DB=moniDB \
  -v ~/postgres_data:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:13
5. Clone this repository
bashCopycd /
git clone https://github.com/danette14/moniDBase.git
6. Initialize the database with the SQL schema
bashCopycd /moniDBase
docker cp init_db.sql my_postgres_db:/init_db.sql
docker exec -it my_postgres_db psql -U myuser -d moniDB -f /init_db.sql
Database Schema
Users Table
ColumnTypeDescriptionuser_idSERIALPrimary key, auto-incrementing IDfull_nameVARCHAR(50)User's full nameusernameVARCHAR(50)Unique username for loginpasswordTEXTEncrypted passwordis_google_userBOOLEANFlag for Google authentication userspictureTEXTURL to user's profile picture
Scans Table
ColumnTypeDescriptionscan_idSERIALPrimary key, auto-incrementing IDurlVARCHAR(50)URL of the scanned websitestatus_codeVARCHAR(10)HTTP status code of the scanssl_statusVARCHAR(20)SSL certificate statusexpiration_dateVARCHAR(30)SSL certificate expiration dateissuerTEXTSSL certificate issueruser_idINTForeign key to users table
Security Notes

For production environments, consider changing the default database credentials
Secure your EC2 instance with appropriate security groups
Consider adding database backups for data persistence

Connecting to the Database
You can connect to the database using the following credentials:

Host: localhost (or your EC2 public IP for remote connections)
Port: 5432
Database: moniDB
Username: myuser
Password: mypassword

Sample connection command:
bashCopypsql -h localhost -p 5432 -U myuser -d moniDB
