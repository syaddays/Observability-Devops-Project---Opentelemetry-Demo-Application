# Implementation Guide

This guide provides a step-by-step walkthrough to deploy the OpenTelemetry Astronomy Shop demo on AWS.

## PART 1: AWS Setup

1.  **Launch AWS EC2 Instance**:
    *   Navigate to AWS Console > EC2 > Instances > Launch Instance.
    *   **Name**: `otel-demo`
    *   **OS**: Ubuntu 22.04 LTS
    *   **Instance Type**: `t2.xlarge` (4 vCPU, 16GB RAM) - *Required for 18+ microservices*.
    *   **Key Pair**: Create new key pair `otel-key` (.pem format).
    *   **Network Settings**: Create security group allowing:
        *   SSH (Port 22)
        *   HTTP (Port 80) - Frontend
        *   Custom TCP (Port 8080) - Load Generator
        *   Custom TCP (Port 4317) - OTLP Receiver
        *   Custom TCP (Port 3001) - Grafana (Optional)
        *   Custom TCP (Port 16686) - Jaeger (Optional)
        *   Custom TCP (Port 9090) - Prometheus (Optional)

## PART 2: Connect to Server

**Option A: Browser Terminal (Easiest)**
1.  Select instance in AWS Console.
2.  Click **Connect** > **EC2 Instance Connect** > **Connect**.

**Option B: SSH Client**
1.  Open terminal.
2.  Set permissions: `chmod 400 ~/Downloads/otel-key.pem`
3.  Connect: `ssh -i ~/Downloads/otel-key.pem ubuntu@<PUBLIC_IP>`

## PART 3: Install Requirements

Update system and install Docker & Docker Compose:

```bash
sudo apt update -y
sudo apt upgrade -y
sudo apt install -y docker.io docker-compose git
```

## PART 4: Clone and Deploy

1.  Clone the repository:
    ```bash
    cd ~
    git clone https://github.com/open-telemetry/opentelemetry-demo.git
    cd opentelemetry-demo
    ```

2.  Start the services (this may take 5-10 minutes):
    ```bash
    sudo docker-compose up -d
    ```

## PART 5: Access Applications

Find your instance's Public IP:
```bash
curl http://169.254.169.254/latest/meta-data/public-ipv4
```

Access the interfaces in your browser:
*   **Web Store**: `http://<PUBLIC_IP>:80`
*   **Grafana**: `http://<PUBLIC_IP>:8080/grafana/` (or port 3001 depending on config)
*   **Jaeger**: `http://<PUBLIC_IP>:8080/jaeger/ui/` (or port 16686)
*   **Prometheus**: `http://<PUBLIC_IP>:8080/prometheus/` (or port 9090)

## PART 6: Explore Grafana

1.  Open Grafana.
2.  Go to **Dashboards** > **Browse**.
3.  Open the **OpenTelemetry Demo** dashboard.
4.  View metrics like Request Rate, Error Rate, and Latency for different services.

## PART 7: Explore Jaeger

1.  Open Jaeger.
2.  Select a service (e.g., `frontend`) from the **Service** dropdown.
3.  Click **Find Traces**.
4.  Click on a trace to view the waterfall diagram of spans, showing how the request traveled through microservices.

## PART 8: Enable Error Flags

Simulate failures to test observability:

1.  Edit the feature flag configuration:
    ```bash
    sudo nano src/flagD/demo.flagd.json
    ```
2.  Change `"defaultVariant": "off"` to `"on"` for a flag like `cartServiceFailure`.
3.  Save and exit (Ctrl+X, Y, Enter).
4.  Restart services:
    ```bash
    sudo docker-compose restart
    ```

## PART 9: Troubleshooting

*   **Check Logs**: `sudo docker-compose logs -f <service_name>`
*   **Check Containers**: `sudo docker ps`
*   **Restart Service**: `sudo docker-compose restart <service_name>`
*   **Stop All**: `sudo docker-compose down`

## PART 10: Cleanup

To avoid AWS charges:
1.  Stop containers: `sudo docker-compose down`
2.  Terminate EC2 instance in AWS Console.
