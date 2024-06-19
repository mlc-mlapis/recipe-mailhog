# Recipe MailHog

Zerops MailHog implementation.

## Zerops YAML import script

The script is intended to create a new project **ghost-app** with four services represented by hostnames: **db** (fully managed [Zerops MariaDB](https://docs.zerops.io/documentation/services/databases/mariadb.html) database), **storage** (fully managed [Zerops S3-compatible](https://docs.zerops.io/documentation/services/storage/s3.html) object storage), **ghost** ([Zerops Node.js-based](https://docs.zerops.io/documentation/services/runtimes/nodejs.html) Ghost application instance), and **mailhog** ([Zerops Go-based](https://docs.zerops.io/documentation/services/runtimes/golang.html) shadow SMTP server). The **db** and **storage** services are created first with `priority: 1`. They have to exist and run before the runtime services **ghost** and **mailhog** will be built, deployed, and run.

```yaml
project:
  name: ghost-app
  tags:
    - Ghost
    - MailHog
    - MariaDB
    - MinIO
services:
  - hostname: db
    type: mariadb@10.6
    mode: NON_HA
    priority: 2
  - hostname: storage
    type: object-storage
    objectStorageSize: 2
    objectStoragePolicy: public-read
    priority: 1
  - hostname: ghost
    type: nodejs@18
    ports:
      - port: 2368
        httpSupport: true
    minContainers: 1
    maxContainers: 1
    buildFromGit: https://github.com/zeropsio/recipe-ghost@main
    enableSubdomainAccess: true
  - hostname: mailhog
    type: go@1
    ports:
      - port: 8025
        httpSupport: true
      - port: 1025
        httpSupport: true
    minContainers: 1
    maxContainers: 1
    buildFromGit: https://github.com/zeropsio/recipe-mailhog@main
    enableSubdomainAccess: true
```
