<h1>System for monitoring</h1>

<h2>How to install?</h2>

<h3>Step 1: Prepare the Environment</h3>

<h4>1. Create a folder for your Docker Compose configuration, for example, monitoring:</h4>

```{bash}
mkdir monitoring
```

<h4>2. Navigate to this folder:</h4>

```{bash}
cd monitoring
```

<h4>3. Git clone</h4>

```{bash}
git clone https://github.com/RKLIT/monitoring-grafna.git
```
<h3>Step 2: Change your settings</h3>

```{bash}
docker-compose.yml
__________________

version: '3.8'
services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    networks:
      - frog_image_share_network
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'

  grafana:
    image: grafana/grafana
    container_name: grafana
    networks:
      - frog_image_share_network
    volumes:
      - grafana-data:/var/lib/grafana
    ports:
      - "3000:3000"

  mysql-exporter:
    image: prom/mysqld-exporter
    container_name: mysql-exporter
    command: 
      - --config.my-cnf=/cfg/.my.cnf
      - --mysqld.address=172.18.0.2:3306
    networks:
      - frog_image_share_network
    volumes:
      - ./.my.cnf:/cfg/.my.cnf
    ports:
      - "9104:9104"


  node-exporter:
    image: prom/node-exporter
    container_name: node-exporter
    networks:
      - frog_image_share_network
    ports:
      - "9100:9100"
    command:
      - '--path.rootfs=/host'
    volumes:
      - /:/host:ro

volumes:
  grafana-data:
  
networks:
 frog_image_share_network:
    external: true
```
<h3>Additional Files</h3>

```{bash}
prometheus.yml
______________

global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']

  - job_name: 'mysql-exporter'
    static_configs:
      - targets: ['mysql-exporter:9104']
  
  - job_name: 'nodeexporter'
    static_configs:
        - targets: ['10.100.2.51:59100']

  - job_name: 'mysqlexporter'
    static_configs:
        - targets: ['10.100.2.51:59104']
```

<h4>MySQL Exporter .my.cnf configuration file: Create a file named .my.cnf in the project root directory with the following content to provide MariaDB credentials:</h4>

```{ini}
.my.cnf
_______

[client]
user=exporter_user
password=exporter_password
```


<h3>If you want to run:</h3>

```{bash}
docker-compose up -d
```

<h3>They used there:</h3>

* **Grafana**

* **Prometheus**

* **Node-Exporter**

* **MySQL-Exporter**

