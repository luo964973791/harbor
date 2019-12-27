### 安装harbor。

```
wget https://github.com/goharbor/harbor/releases/download/v1.9.3/harbor-offline-installer-v1.9.3.tgz
```

### 进入目录

```
tar zxvf harbor-offline-installer-v1.9.3.tgz && cd harbor
```

### vi harbor.yml修改配置文件

```
hostname: 172.27.0.6  #修改第五行改成主机IP.
port: 888  #修改第十行
harbor_admin_password: 12345     #修改二十七行改密码
```

### 启动。

```
sh install.sh
```

