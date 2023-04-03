# Настройка облачной инфраструктуры

## Создание бакета

Создаём бакет с помощью `s3cmd`

```shell
> s3cmd mb s3://melnikov-mlops

Bucket 's3://melnikov-mlops/' created
```

Открываем бакет для чтения с помощью `yc`

```shell
> yc storage bucket update --name melnikov-mlops --public-read --folder-id=b1g1enffqq7ql8kv0d6q

name: melnikov-mlops
folder_id: b1g1enffqq7ql8kv0d6q
anonymous_access_flags:
  read: true
default_storage_class: STANDARD
versioning: VERSIONING_DISABLED
acl: {}
created_at: "2023-04-03T06:08:06.029439Z"
```

## Создание Spark-кластера

```shell
> yc dataproc cluster create melnikov-dpc \
   --folder-id=b1g1enffqq7ql8kv0d6q \
   --bucket=melnikov-mlops \
   --zone=ru-central1-b \
   --service-account-name=assistant \
   --ssh-public-keys-file=/home/miller/.ssh/id_rsa.pub \
   --subcluster name=dpc-master,`
               `role=masternode,`
               `resource-preset=s3-c2-m8,`
               `disk-type=network-ssd,`
               `disk-size=40,`
               `subnet-name=default-ru-central1-b,`
               `assign-public-ip=true \
   --subcluster name=dpc-data,`
               `role=datanode,`
               `resource-preset=s3-c4-m16,`
               `disk-type=network-hdd,`
               `disk-size=128,`
               `subnet-name=default-ru-central1-b,`
               `hosts-count=3,`
               `assign-public-ip=false \
   --deletion-protection=false \
   --ui-proxy=true \
   --security-group-ids=<>
```