### 必须提前部署docker

```
yum install gcc gcc-c++ make cmake python-pip -y && yum install -y python-devel
pip install docker-compose
wget https://github.com/goharbor/harbor/releases/download/v1.10.2/harbor-online-installer-v1.10.2.tgz
```

### 进入目录

```
tar zxvf harbor-online-installer-v1.10.2.tgz && cd harbor
```

### vi harbor.yml修改配置文件

```
hostname: 172.27.0.6  #修改第五行改成主机IP.
port: 888  #修改第六行
harbor_admin_password: 12345     #修改二十七行改密码
#https: #关闭证书.
  # https port for harbor, default is 443
  #port: 443
  # The path of cert and key files for nginx
  #certificate: /your/certificate/path
  #private_key: /your/private/key/path
```

### 启动。

```
sh install.sh
#配置开机自启动
cat <<EOF | sudo tee /etc/systemd/system/harbor.service 
[Unit]
Description=harbor
After=docker.service systemd-networkd.service systemd-resolved.service
Requires=docker.service
Documentation=http://github.com/vmware/harbor

[Service]
Type=simple
Restart=on-failure
RestartSec=5
ExecStart=/usr/local/bin/docker-compose -f  /root/harbor/docker-compose.yml up
ExecStop=/usr/local/bin/docker-compose -f  /root/harbor/docker-compose.yml down

[Install]
WantedBy=multi-user.target
EOF

systemctl enable harbor.service
```


### 配置后端minio S3存储.
```
vi harbor.yml
storage_service:
  s3:
    accesskey: minio
    secretkey: minio123
    region: us-east-1
    regionendpoint: http://172.27.0.6:9000
    bucket: harbor
    secure: false
    v4auth: true
```

