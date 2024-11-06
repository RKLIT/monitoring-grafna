<h1>System for monitoring</h1>

<h2>How to install?</h2>

<h3>1. Git clone</h3>

```{bash}
git clone https://github.com/RKLIT/monitoring-grafna.git
```
<h3>2. Change your settings</h3>

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

<h3>If you want to run:</h3>

```{bash}
docker-compose up -d
```

<h4>This is repository works with frog infrastraction, so you will need to do changes anyway.</h4>  

<h3>They used there:</h3>

* **Grafana**

* **Prometheus**

* **Node-Exporter**

* **MySQL-Exporter**

