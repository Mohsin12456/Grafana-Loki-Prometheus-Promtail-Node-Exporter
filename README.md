# Grafana-Loki-Prometheus-Promtail-Node-Exporter

### Server 1: Monitoring Server

### Step 1: Set Up Docker

1. **Install Docker and Docker Compose**
    - Update the package list:
        
        ```bash
        sudo apt update
        ```
        
    - Install Docker:
        
        ```bash
        sudo apt install -y docker.io
        ```
        
    - Install Docker Compose:
        
        ```bash
        sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
        sudo chmod +x /usr/local/bin/docker-compose
        ```
        

### Step 2: Set Up Grafana, Loki, and Prometheus

1. **Create a Docker Compose File**
    
    Create a file named `docker-compose.yml`:
    
2. **Create Configuration Files**
    - **Prometheus Configuration (`prometheus.yml`):**
    - **Loki Configuration (`loki-config.yaml`):**
3. **Start the Containers**
    
    ```bash
    docker-compose up -d
    ```
    

### Server 2: Secondary Server

### Step 1: Set Up Docker

Follow the same steps as on the monitoring server to install Docker and Docker Compose.

### Step 2: Set Up Apache2, Promtail, and Node Exporter

1. **Create a Docker Compose File**
    
    Create a file named `docker-compose.yml`:
    
2. **Create Promtail Configuration File**
    - **Promtail Configuration (`promtail-config.yaml`):**
3. **Start the Containers**
    
    ```bash
    docker-compose up -d
    ```
    

### Grafana Dashboard Setup

### Step 1: Access Grafana

1. Open a web browser and go to `http://monitoring-server-ip:3000`.
2. Login with the default credentials (`admin/admin`) and change the password if prompted.

### Step 2: Add Data Sources

1. **Add Prometheus:**
    - Go to `Configuration` (gear icon) > `Data Sources`.
    - Click `Add data source` and select `Prometheus`.
    - Set the URL to `http://monitoring-server-ip:9090` and click `Save & Test`.
2. **Add Loki:**
    - Go to `Configuration` > `Data Sources`.
    - Click `Add data source` and select `Loki`.
    - Set the URL to `http://monitoring-server-ip:3100` and click `Save & Test`.

### Step 3: Import Dashboard

1. Go to `Create` (plus icon) > `Import`.
2. Enter the dashboard ID or JSON file from Grafana.com. You can use this example dashboard for Apache and Node Exporter metrics.
3. Click `Load`, set the Prometheus and Loki data sources, and click `Import`.
4. **Create Alerting Rules**
    - Go to `Alerting` (bell icon) > `Alerting rules` and click `New alert rule`.
    - **For CPU Usage Alert:**
        - In the `Alert Rule` tab, set the `Query` to the Prometheus expression you used in `alert.rules.yml` (e.g., `avg(rate(node_cpu_seconds_total{job="node_exporter", mode="user"}[1m])) by (instance)`).
        - Set the `Condition` to be greater than `0.9` over `5 minutes`.
        - Configure `Labels` and `Annotations` as needed.
    - **For Memory Usage Alert:**
        - Similarly, set the `Query` to `(node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)`.
        - Set the `Condition` to be less than `0.1` over `5 minutes`.
        - Configure `Labels` and `Annotations` accordingly.
    - **For Disk Space Alert:**
        - Set the `Query` to `(node_filesystem_avail_bytes / node_filesystem_size_bytes)`.
        - Set the `Condition` to be less than `0.1` over `5 minutes`.
        - Configure `Labels` and `Annotations` as needed.
5. **Configure Notification Channels**
    - Go to `Alerting` (bell icon) > `Notification channels`.
    - Click `Add channel` and select `Email` (or another notification type like Slack or PagerDuty).
    - Configure your email settings, including SMTP server details, to receive alerts via email.
    - Save the notification channel.
6. **Assign Notification Channels to Alerts**
    - Go back to your alerting rules and assign the notification channel(s) to each alert rule.

### Step 3: Test Alerts

1. **Simulate High CPU Usage**
    - Use `stress-ng` or a similar tool on the secondary server to generate high CPU load, as described in the previous answer.
2. **Verify Alerts in Grafana**
    - Monitor the Grafana `Alerting` page to see if your alert triggers.
    - Check the `Alerting` tab on your Grafana dashboard to verify the state of your alerts.
3. **Verify Email Notifications**
    - Check your email to ensure you receive notifications about high CPU, memory usage, or low disk space.

### Final Steps

1. **Verify Logs in Grafana:**
    - Go to the `Explore` section in Grafana and select `Loki` to view logs from Promtail.
2. **Verify Metrics:**
    - Go to the Prometheus section and check if metrics are being scraped correctly.
