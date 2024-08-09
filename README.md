### 必须提前部署docker,并设置证书

```
yum install gcc gcc-c++ make cmake python3.11-pip openssl openssl-devel  -y && yum install -y python3.11-devel
pip install docker-compose
[ ! -d "/data" ] && mkdir /data; cd /data
openssl req -newkey rsa:4096 -nodes -sha256 -keyout harbor.key -x509 -days 36500 -out harbor.crt -subj "/CN=$(hostname -I | awk '{print $1}')" -addext "subjectAltName=IP:$(hostname -I | awk '{print $1}')"

#注意这里的IP 上上面一步harbor 生成证书的IP,所有dockerk客户端的路径都必须一致，比如我的harbor服务器的IP是172.27.0.3，所有的服务器下面创建目录的IP都必须是harbor服务器的IP.
mkdir -p /etc/docker/certs.d/172.27.0.3  && cd /etc/docker/certs.d/172.27.0.3 && cp /data/harbor.crt ./ca.crt
systemctl daemon-reload && systemctl restart docker
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
https:
  # https port for harbor, default is 443
  port: 443
  # The path of cert and key files for nginx
  certificate: /data/harbor.crt
  private_key: /data/harbor.key
```

### 启动。

```
sh install.sh
#配置开机自启动
(crontab -l ; echo "@reboot sleep 60 && cd /root/harbor && /usr/local/bin/docker-compose restart") | crontab -
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

