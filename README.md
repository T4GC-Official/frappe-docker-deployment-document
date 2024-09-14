# frappe-docker-deployment-document

Deploying Frappe in Docker can streamline development and deployment processes. Below are the steps to deploy Frappe in Docker:

### 1. **Install Docker and Docker Compose**
Ensure you have Docker and Docker Compose installed on your system:
- [Install Docker](https://docs.docker.com/get-docker/)
- [Install Docker Compose](https://docs.docker.com/compose/install/)

### 2. **Clone Frappe Docker Repository**
Frappe maintains an official Docker repository for easy setup.

```bash
git clone https://github.com/frappe/frappe_docker
cd frappe_docker
```

### 3. **Set Up Environment Variables**
You can define environment variables by copying the sample `.env` file and modifying it as needed.

```bash
cp example.env .env
```

Update the `.env` file with your configurations, such as:

```bash
MYSQL_ROOT_PASSWORD=root
ADMIN_PASSWORD=admin
SITE_NAME=your-site-name.local
INSTALL_APPS=erpnext
```

### 4. **Set Up Custom Docker Compose File (Optional)**
You can create a custom `docker-compose.yml` based on your environment. The example `docker-compose.yml` might look like this:

```yaml
version: '3'

services:
  mariadb:
    image: mariadb
    environment:
      MYSQL_ROOT_PASSWORD: root
    volumes:
      - ./mariadb_data:/var/lib/mysql

  redis-cache:
    image: redis:alpine

  redis-queue:
    image: redis:alpine

  redis-socketio:
    image: redis:alpine

  backend:
    image: frappe/erpnext-worker:latest
    environment:
      START_MARIADB: 0
    volumes:
      - ./sites:/home/frappe/frappe-bench/sites

  frontend:
    image: frappe/erpnext-nginx:latest
    ports:
      - "8080:80"
    depends_on:
      - backend
    volumes:
      - ./sites:/home/frappe/frappe-bench/sites

  socketio:
    image: frappe/frappe-socketio:latest
    depends_on:
      - redis-socketio

  worker-default:
    image: frappe/erpnext-worker:latest
    command: worker
    depends_on:
      - backend
```

### 5. **Start the Containers**
Now that your configuration is ready, you can start the Frappe instance using Docker Compose:

```bash
docker-compose up -d
```

This will spin up the required services (e.g., MariaDB, Redis, backend, frontend, workers, etc.).

### 6. **Create a New Site**
Once the containers are up and running, you can create a new Frappe site using the `docker exec` command:

```bash
docker exec -it <backend_container_name> bash
bench new-site your-site-name.local
```

### 7. **Install Frappe/ERPNext**
After creating the site, install Frappe or ERPNext:

```bash
bench --site your-site-name.local install-app erpnext
```

### 8. **Access Frappe**
You should be able to access the Frappe application on `http://localhost:8080`.

---

This basic setup can be expanded depending on your needs (e.g., setting up SSL, customizing worker configurations, etc.). Let me know if you need help with those additional steps.
