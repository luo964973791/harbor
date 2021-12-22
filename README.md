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


### 配置后端minio S3存储.
```
mc config host add minio http://172.27.0.6:9000 minio minio123
mc mb minio/harbor harbor
mc admin user add minio harbor minio123

#编辑harbor.json
vi harbor.json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation",
        "s3:ListBucketMultipartUploads"
      ],
      "Resource": "arn:aws:s3:::harbor"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:DeleteObject",
        "s3:ListMultipartUploadParts",
        "s3:AbortMultipartUpload"
      ],
      "Resource": "arn:aws:s3:::harbor/*"
    }
  ]
}


mc admin policy add minio harbor ./harbor.json
mc admin policy set minio harbor user=harbor
```

