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
```

