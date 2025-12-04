# OpenTelemetry Observability Project

Complete hands-on implementation guide for deploying the OpenTelemetry Astronomy Shop demonstration application on AWS. This project showcases containerized microservices with comprehensive observability using metrics, traces, and logs.

## Project Overview

This implementation deploys 18+ microservices written in 10 different programming languages (Java, Python, Go, JavaScript, .NET, Node.js, etc.) to demonstrate real-world observability practices using OpenTelemetry.

### Key Features

- **Microservices Architecture**: 18+ interdependent services in a single deployment
- **Multi-Language Support**: Services in Java, Python, Go, JavaScript, .NET, Node.js
- **Complete Observability Stack**:
  - Metrics collection via Prometheus
  - Distributed tracing via Jaeger
  - Log aggregation via OpenSearch
  - Visualization via Grafana
- **Error Injection Testing**: Feature flags to simulate failures
- **Cloud Deployment**: AWS EC2 infrastructure
- **Docker Containerization**: Complete container orchestration

## What You Will Learn

- Observability concepts (metrics, logs, traces)
- OpenTelemetry architecture and usage
- Docker and Docker Compose
- Grafana dashboard creation
- Jaeger distributed tracing
- Root cause analysis in distributed systems
- AWS EC2 management
- Microservices debugging techniques

## Prerequisites

- AWS account with EC2 access
- SSH client (built-in on Mac/Linux, PuTTY on Windows)
- Web browser
- Basic Linux command-line knowledge

## Technology Stack

| Component | Purpose | Technology |
|-----------|---------|------------|
| Telemetry Collection | Data ingestion | OpenTelemetry Collector |
| Metrics Storage | Time-series data | Prometheus |
| Metrics Visualization | Dashboards | Grafana |
| Trace Storage | Request tracking | Jaeger |
| Log Storage | Event records | OpenSearch |
| Microservices | Business logic | 10+ languages |
| Container Orchestration | Service management | Docker Compose |
| Message Queue | Event streaming | Kafka |

## Quick Start

### 1. Launch AWS EC2 Instance

Navigate to AWS Console:
- Search for EC2
- Click Instances → Launch Instance
- Configure:
  - Name: otel-demo
  - OS: Ubuntu 22.04 LTS
  - Instance Type: t2.xlarge (16GB RAM, 4 vCPU)
  - Create new key pair: otel-key (.pem format)
  - Create security group with inbound rules:
    - SSH (Port 22)
    - HTTP (Port 80)
    - Custom TCP (Port 8080)
    - Custom TCP (Port 4317)

### 2. Connect to Server

Browser Terminal (Recommended):
- Select instance → Click Connect → EC2 Instance Connect → Connect

Or SSH:
```bash
chmod 400 ~/Downloads/otel-key.pem
ssh -i ~/Downloads/otel-key.pem ubuntu@<PUBLIC_IP>
```

### 3. Install Requirements

```bash
sudo apt update -y
sudo apt upgrade -y
sudo apt install -y docker.io docker-compose git
```

### 4. Clone and Deploy

```bash
cd ~
git clone https://github.com/open-telemetry/opentelemetry-demo.git
cd opentelemetry-demo
sudo docker-compose up -d
```

### 5. Access Applications

Find your instance Public IP:
```bash
curl http://169.254.169.254/latest/meta-data/public-ipv4
```

Access in browser:
- **Frontend Store**: http://<IP>:80
- **Grafana Dashboards**: http://<IP>:3001 (admin/admin)
- **Jaeger Tracing**: http://<IP>:16686
- **Prometheus Metrics**: http://<IP>:9090
- **Load Generator**: http://<IP>:8089

### 6. Explore Observability

**Grafana**: View metrics across all services
- Select Demo Dashboard
- Choose service from dropdown
- Observe latency, error rate, request rate

**Jaeger**: Trace request flow
- Select service
- Click "Find Traces"
- Click trace to see complete request path
- Red spans indicate errors

## Architecture

```
Microservices (18+ services)
         ↓
OpenTelemetry SDK
         ↓
OpenTelemetry Collector
         ↓
    ├─ Prometheus (Metrics)
    ├─ Jaeger (Traces)
    └─ OpenSearch (Logs)
         ↓
    ├─ Grafana (Dashboards)
    ├─ Jaeger UI (Traces)
    └─ OpenSearch UI (Logs)
```

## Microservices Deployed

| Service | Language | Function |
|---------|----------|----------|
| adservice | Java | Advertisement recommendations |
| frontend | JavaScript | Web UI |
| frontendproxy | Go | API gateway |
| cartservice | .NET | Shopping cart |
| checkoutservice | Go | Order processing |
| paymentservice | JavaScript | Payment handling |
| shippingservice | Go | Shipping logistics |
| productcatalogservice | Go | Product information |
| currencyservice | Node.js | Currency conversion |
| recommendationservice | Python | Product recommendations |
| accountingservice | .NET | Financial tracking |
| emailservice | Python | Email notifications |
| orderservice | Java | Order management |
| loadgenerator | Python | Synthetic traffic |
| otelcol | Go | Telemetry collection |
| prometheus | Prometheus | Metrics storage |
| grafana | Grafana | Visualization |
| jaeger | Jaeger | Trace storage |
| kafka | Kafka | Event streaming |
| opensearch | OpenSearch | Log storage |

## Advanced Usage

### Enable Error Flags

Create intentional failures to test observability:

```bash
cd ~/opentelemetry-demo
sudo nano src/flagD/demo.flagd.json
```

Change "defaultVariant": "off" to "on" for flags like:
- adServiceHighCPU
- cartServiceFailure
- recommendationServiceFailure

Restart services:
```bash
sudo docker-compose down
sudo docker-compose up -d
```

### Common Commands

```bash
# View running containers
sudo docker ps

# View service logs
sudo docker logs <service_name>

# Access container shell
sudo docker exec -it <service_name> /bin/bash

# View all logs
sudo docker-compose logs -f

# Restart specific service
sudo docker-compose restart <service_name>

# Check system resources
free -h
top
```

## Troubleshooting

### Services Crashing

Check logs:
```bash
sudo docker logs <service_name>
```

### Port Already in Use

Find and kill process:
```bash
sudo lsof -i :8080
sudo kill -9 <PID>
```

### No Data in Grafana

1. Wait 2-3 minutes for data accumulation
2. Place orders on frontend to generate traffic
3. Check collector:
```bash
sudo docker logs otelcol | tail -20
```

### High Resource Usage

Check RAM availability:
```bash
free -h
```

If insufficient, reduce Kafka memory or upgrade instance type.

## Key Concepts

### Observability Pillars

**Metrics**: Numerical performance data over time
- Latency: Response time (P50, P95, P99)
- Error Rate: Percentage of failed requests
- Request Rate: Requests per second
- Resource Usage: CPU, memory, disk

**Logs**: Detailed event records
- Timestamp: When event occurred
- Service: Which service generated it
- Level: Severity (error, warning, info, debug)
- Message: Event description

**Traces**: Complete request flow across services
- Trace ID: Unique request identifier
- Span: Single operation
- Span ID: Unique operation identifier
- Duration: Operation time
- Status: Success or error

### OpenTelemetry Collector Components

**Receivers**: Collect data
- OTLP: OpenTelemetry protocol (primary)
- Jaeger: Distributed tracing format
- Prometheus: Metrics scraping

**Processors**: Transform data
- Batch: Group data for efficiency
- Sampling: Reduce data volume
- Memory Limiter: Prevent resource exhaustion

**Exporters**: Send to backends
- Prometheus: Metrics storage
- Jaeger: Trace storage
- OpenSearch: Log storage
- Cloud platforms: Datadog, New Relic, Splunk, etc.

## Configuration Files

### Docker Compose

**File**: docker-compose.yml

Defines all services, networks, ports, and dependencies. Contains configuration for:
- 18+ microservices
- Monitoring stack (Prometheus, Grafana, Jaeger)
- Infrastructure (Kafka, OpenSearch, Zookeeper)
- Network communication
- Port mappings
- Volume mounts

### OpenTelemetry Collector Config

**File**: src/otelcollector/otel-collector-config.yml

Configures telemetry collection:
- Data receivers (OTLP, Jaeger, Prometheus)
- Data processors (batch, sampling, resource decorators)
- Exporters to multiple backends
- Logging and debugging

### Feature Flags

**File**: src/flagD/demo.flagd.json

Enables controlled failure injection:
- adServiceHighCPU: Increase CPU to demonstrate performance issues
- cartServiceFailure: Simulate cart service errors
- recommendationServiceFailure: Fail recommendation engine
- productCatalogFailure: Fail product lookups

## Cleanup

### Stop Services

```bash
sudo docker-compose down
```

### Delete All Data

```bash
sudo docker-compose down -v
```

### Stop AWS Instance

AWS Console → Select instance → Instance State → Stop

### Terminate AWS Instance

AWS Console → Select instance → Instance State → Terminate Instance

## Cost Considerations

- **Compute**: t2.xlarge at ~USD 0.15/hour
- **Storage**: 15 GB EBS at ~USD 1.50/month
- **Estimated Project Cost**: USD 5-10 for 2-3 hours

## Next Steps

1. **Integrate Different Backends**: Modify collector config to use Datadog, New Relic, or Splunk
2. **Deploy on Kubernetes**: Use k8s manifests instead of Docker Compose
3. **Customize Microservices**: Add business logic to existing services
4. **Create Custom Dashboards**: Build additional Grafana dashboards
5. **Implement Alerting**: Configure alerts based on SLOs

## Documentation

- [Official OpenTelemetry Docs](https://opentelemetry.io/)
- [OpenTelemetry Demo Repository](https://github.com/open-telemetry/opentelemetry-demo)
- [Grafana Documentation](https://grafana.com/docs/)
- [Jaeger Tracing Docs](https://www.jaegertracing.io/docs/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)

## Project Files

This repository includes:

- `README.md` - Main documentation (this file)
- `IMPLEMENTATION_GUIDE.md` - Step-by-step implementation instructions
- `ARCHITECTURE.md` - Detailed architecture and components
- `TROUBLESHOOTING.md` - Common issues and solutions
- `REFERENCE.md` - Command reference and quick lookup
- `.gitignore` - Git ignore rules
- `LICENSE` - MIT License

## Related Projects

- [OpenTelemetry GitHub](https://github.com/open-telemetry)
- [Grafana Stack](https://grafana.com/)
- [Jaeger Tracing](https://www.jaegertracing.io/)
- [Prometheus](https://prometheus.io/)

## Contributing

To contribute improvements:

1. Fork the repository
2. Create a feature branch
3. Make improvements
4. Submit pull request with description

## License

This project is licensed under the MIT License. See LICENSE file for details.

## Support

For issues or questions:

1. Check TROUBLESHOOTING.md
2. Review official OpenTelemetry documentation
3. Open GitHub issue with detailed description
4. Reference video tutorial: Cloud Champ OpenTelemetry Demo

## Video Reference

This implementation is based on:
- **Channel**: Cloud Champ
- **Video**: Observability DevOps Project - OpenTelemetry Demo Application
- **URL**: https://www.youtube.com/watch?v=nWDCWe5OwjQ

## Acknowledgments

- OpenTelemetry Project for the demo application
- Cloud Champ for the excellent tutorial
- Open-source community for all tools

## Keywords

DevOps, Observability, OpenTelemetry, Microservices, Docker, Kubernetes, Prometheus, Grafana, Jaeger, AWS, Distributed Tracing, Monitoring, Cloud Native

---

**Version**: 1.0
**Last Updated**: December 4, 2024
**Status**: Production Ready
