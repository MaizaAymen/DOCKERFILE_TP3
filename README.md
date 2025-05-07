---
title: "Dockerized PHP Application"
author: "Aymen"
date: "`r Sys.Date()`"
output: html_document
---

# Project Overview

This repository contains a simple PHP web application served by Apache, packaged into a Docker image. It demonstrates how to containerize a PHP project and initialize a MySQL database schema.

# Directory Structure

```
.
├── Dockerfile
├── app/
│   ├── index.html
│   ├── index.php
│   └── db-config.php
└── db/
    └── articles.sql
```

# Prerequisites

- Docker installed on your machine
- (Optional) Docker Compose if orchestrating multiple containers

# Building the Docker Image

```bash
# Build the PHP/Apache image
docker build -t php-app-image .
```

# Running the Container

```bash
# Run the container, exposing port 80 inside to port 8080 on host
docker run -d --name php-app -p 8080:80 php-app-image
```

Visit http://localhost:8080 in your browser to see the application.

# Database Initialization

The SQL script (`db/articles.sql`) defines the initial schema and data for the `test` database. To initialize MySQL:

```bash
# Start a MySQL container
docker run -d --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=test \
  mysql:8.0

# Import the SQL script into the 'test' database
docker exec -i mysql-db mysql -u root -proot test < db/articles.sql
```

> Note: The `COPY db/articles.sql /docker-entrypoint-initdb.d/` step in the Dockerfile only applies if you use the official MySQL image. For a multi-container setup, consider using Docker Compose.

# Configuration

Database connection settings are defined in `app/db-config.php`:

```php
const DB_DSN  = 'mysql:host=localhost;dbname=test';
const DB_USER = 'test';
const DB_PASS = 'test';
```

Ensure these credentials match your MySQL container or server.

# Usage

- Place PHP files in `app/` as needed.
- Update SQL schema in `db/articles.sql` and re-import as required.

# Cleanup

```bash
docker stop php-app mysql-db
docker rm php-app mysql-db
docker rmi php-app-image
```
