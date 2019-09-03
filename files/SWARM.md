# Example Services 

## Three node systemctl example

```
[Unit]
Description=etcd key-value store
Documentation=https://github.com/etcd-io/etcd
After=network.target

[Service]
User=etcd
Type=notify
Environment=ETCD_DATA_DIR=/var/lib/etcd
Environment=ETCD_NAME={{ ansible_hostname }}
ExecStart=/usr/local/bin/etcd --name ${ETCD_NAME} --initial-advertise-peer-urls http://{{ ansible_default_ipv4.address }}:2380 \
  --listen-peer-urls http://10.1.1.80:2380 \
  --listen-client-urls http://10.1.1.80:2379,http://127.0.0.1:2379 \
  --advertise-client-urls http://10.1.1.80:2379 \
  --initial-cluster-token etcd-cluster-1 \
  --initial-cluster swarm01srv=http://10.1.1.80:2380,swarm02srv=http://10.1.1.81:2380,swarm03srv=http://10.1.1.86:2380 \
  --initial-cluster-state new \
  --heartbeat-interval 1000 \
  --election-timeout 5000
Restart=always
RestartSec=10s
LimitNOFILE=40000

[Install]
[Unit]
Description=etcd key-value store
Documentation=https://github.com/etcd-io/etcd
After=network.target

[Service]
User=etcd
Type=notify
Environment=ETCD_DATA_DIR=/var/lib/etcd
Environment=ETCD_NAME={{ ansible_hostname }}
ExecStart=/usr/local/bin/etcd --name ${ETCD_NAME} --initial-advertise-peer-urls http://{{ ansible_default_ipv4.address }}:2380 \
  --listen-peer-urls http://10.1.1.81:2380 \
  --listen-client-urls http://10.1.1.81:2379,http://127.0.0.1:2379 \
  --advertise-client-urls http://10.1.1.81:2379 \
  --initial-cluster-token etcd-cluster-1 \
  --initial-cluster swarm01srv=http://10.1.1.80:2380,swarm02srv=http://10.1.1.81:2380,swarm03srv=http://10.1.1.86:2380 \
  --initial-cluster-state new \
  --heartbeat-interval 1000 \
  --election-timeout 5000
Restart=always
RestartSec=10s
LimitNOFILE=40000

[Install]
[Unit]
Description=etcd key-value store
Documentation=https://github.com/etcd-io/etcd
After=network.target

[Service]
User=etcd
Type=notify
Environment=ETCD_DATA_DIR=/var/lib/etcd
Environment=ETCD_NAME={{ ansible_hostname }}
ExecStart=/usr/local/bin/etcd --name ${ETCD_NAME} --initial-advertise-peer-urls http://{{ ansible_default_ipv4.address }}:2380 \
  --listen-peer-urls http://10.1.1.86:2380 \
  --listen-client-urls http://10.1.1.86:2379,http://127.0.0.1:2379 \
  --advertise-client-urls http://10.1.1.86:2379 \
  --initial-cluster-token etcd-cluster-1 \
  --initial-cluster swarm01srv=http://10.1.1.80:2380,swarm02srv=http://10.1.1.81:2380,swarm03srv=http://10.1.1.86:2380 \
  --initial-cluster-state new \
  --heartbeat-interval 1000 \
  --election-timeout 5000
Restart=always
RestartSec=10s
LimitNOFILE=40000

[Install]
```