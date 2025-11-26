import subprocess
import time
from prometheus_client import start_http_server, Gauge

# Prometheus Metrics
g_packet_loss = Gauge('icmp_packet_loss', 'Packet loss percentage', ['target'])
g_latency = Gauge('icmp_latency_ms', 'Average ICMP latency in ms', ['target'])

# ping 함수
def ping_target(target):
    try:
        cmd = ["ping", "-c", "4", "-W", "1", target]
        result = subprocess.run(cmd, capture_output=True, text=True)
        output = result.stdout

        # packet loss 파싱
        loss_line = [l for l in output.split("\n") if "packet loss" in l][0]
        loss_percent = float(loss_line.split("%")[0].split()[-1])

        # latency(rtt) 파싱
        rtt_line = [l for l in output.split("\n") if "rtt min" in l]
        if not rtt_line:
            return loss_percent, None

        # rtt min/avg/max/mdev = 32.029/32.140/32.252/0.111 ms
        rtt_values = rtt_line[0].split("=")[1].strip().split("/")
        avg_latency = float(rtt_values[1])

        return loss_percent, avg_latency

    except Exception as e:
        print("PING PARSE ERROR:", e)
        return 100.0, None

def main():
    targets = [
        "8.8.8.8",
        "1.1.1.1"
    ]

    print("Starting ICMP Exporter on port 9105...")
    start_http_server(9105)

    while True:
        for target in targets:
            loss, latency = ping_target(target)

            g_packet_loss.labels(target=target).set(loss)
            if latency is None:
                g_latency.labels(target=target).set(-1)
            else:
                g_latency.labels(target=target).set(latency)

            print(f"[{target}] loss={loss}, latency={latency}")

        time.sleep(5)

if __name__ == "__main__":
    main()
