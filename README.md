<img width="1673" height="742" alt="2025-11-25 15 25 32" src="https://github.com/user-attachments/assets/15fe6ff5-3948-48af-a587-a89bd85f2950" />

---

# ICMP Monitoring System
Prometheus + Grafana + Custom Python ICMP Exporter

이 프로젝트는 서버 또는 네트워크 장비의 ICMP Ping Latency / Packet Loss를 모니터링하기 위한 시스템입니다.  
Docker Compose 기반으로 Prometheus, Grafana, ICMP Exporter 환경을 자동 구성하여 네트워크 품질을 실시간으로 시각화할 수 있습니다.

---
<br>

## Requirements

### Docker 설치

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
```
### Docker Compose 설치
```bash
sudo apt install -y docker-compose
```
### Git 설치
```bash
sudo apt install -y git
```
<br>

## Installation
```bash
git clone https://github.com/allenopark/icmp-monitoring.git
cd icmp-monitoring
```
<br>

## Directory Structure
```bash
icmp-monitoring/
├── docker-compose.yml
├── exporter/
│   ├── icmp_exporter.py
│   └── requirements.txt
├── grafana/
│   ├── dashboards/
│   │   └── icmp_dashboard.json
│   └── provisioning/
│       ├── dashboards/
│       │   └── dashboards.yaml
│       └── datasources/
│           └── datasource.yaml
└── prometheus.yml
```
<br>

## Configure ICMP Source IP Address
ICMP 출발지 IP 주소 (코드가 실행되는 서버 IP) 를 아래와 같이 설정합니다. 

```bash
nano ~/icmp-monitoring/prometheus.yml
```
예시:

```bash
scrape_configs:
  - job_name: "icmp_exporter"
    static_configs:
      - targets: ["<server-IP>:9105"]
```

<br>

## Adding More Ping Targets
Ping 대상은 아래 파일에서 설정합니다:

```bash
nano ~/icmp-monitoring/exporter/icmp_exporter.py
```
예시:

```bash
targets = [
    "8.8.8.8",
    "1.1.1.1",
]
```
<br>


## Running the System
아래 명령어로 서비스를 실행할 수 있습니다:
```bash
cd ~/icmp-monitoring/
docker compose up -d --build
```
컨테이너가 정상 실행 중인지 확인:
```bash
docker ps
```

<br>

## Accessing the Services
### Grafana
#### 웹 접속 
```bash
http://<server-ip>:3000
```
#### 로그인 
<기본 계정><br>
ID: admin<br>
PW: admin

#### Dashboards 실행
```bash
메뉴 : Grafana → Dashboards → ICMP Monitoring Dashboad 클릭 
```

