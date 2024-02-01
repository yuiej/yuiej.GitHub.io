---
title: icloudpd 部署
date: 2024-02-01 13:39:27
tags: 
  - 树莓派
  - Raspberry Pi
  - iCloud
  - photo
  - 同步
categories: 
  - [Linux,Raspberry Pi]
comments: true
cover: img/cover/20151120-012030-Lakeside-Cilaos-2015.jpg
---
## 简介

这是一个用于 iCloud 照片下载的Alpine Linux Docker 容器。我用它来将我家中所有iCloud的照片流同步回我的服务器。然后搭配photoprism 即可完美搭建自己的家庭相册，非常的nice！

## 前提条件

**你拥有一台能够运行docker，并且已经安装了docker、docker-compose的设备**

## 创建目录

```shell
sudo mkdir -p /www/server/icloudpd
sudo chmod -R 755 /www/server/icloudpd
```

## 创建配置文件user1.env

一个用户创建一个配置，如有第二个用户则创建 第二个配置文件**user2.env**

```
# iCloudPD environment variables
# Variables with values beneath are showing the defaults used by the script if not specified

# User and group details for account
user=user1
user_id=1000
#group=group
group_id=1000

## Apple iCloud Photo Downloader
# Apple ID credentials 填上自己的apple id
apple_id=xxxx@xxxx.com

# Authentication type 2FA or Web
authentication_type=2FA

# Folder structure for downloaded files
folder_structure={:%Y/%m}

# Permissions for directories and files
directory_permissions=755
file_permissions=755

# Creates a JPEG copy of any downloaded HEIC files (set empty to disable)
#convert_heic_to_jpeg=

# Delete JPEG files if associated HEIC file is removed (set empty to disable)
#delete_heic_jpegs=

# Additional command line options to pass to iCloud Photo Downloader eg: --auto-delete --set-exif-datetime
#command_line_options=

# Syncronisation interval. 21600=6hr, 43200=12hr, 86400=24hr
#synchronisation_interval=86400

# Set the number of days before 2FA expires to start ending notifications
#notification_days=80

# Set notification type to Prowl, Pushbullet or Telegram
#notification_type=

# The API key for Prowl, Pushbullet and Telegram notifications
#prowl_api_key=
#pushbullet_api_key=
#telegram_token=
#telegram_chat_id=

icloud_china=true
auth_china=true
```



## 创建docker-compose.yml

如果只有一个需要同步，则删掉下面

```yaml
version: "3.8"

services:
   icloudpd_user1:
      hostname: icloudpd_user1
      environment:
         - TZ=Asia/Shanghai
      image: boredazfcuk/icloudpd
      healthcheck:
         test: /usr/local/bin/healthcheck.sh
         start_period: 30s
      restart: always
      volumes:
         - ./icloudpd_user1_config:/config
         - /media/saber/saber/nextcloud/data/iCould-yujie/:/home/user1/iCloud/
      env_file: 
         - ./user1.env
   # 第二个用户的配置，不需要则删掉下面
   icloudpd_user2:
      hostname: icloudpd_user2
      environment:
         - TZ=Asia/Shanghai
      image: boredazfcuk/icloudpd
      healthcheck:
         test: /usr/local/bin/healthcheck.sh
         start_period: 30s
      restart: always
      volumes:
         - ./icloudpd_user2_config:/config
         - /media/saber/saber/nextcloud/data/iCould-enya/:/home/user2/iCloud/
      env_file: 
         - ./user2.env
```



## 启动

```shell
sudo docker-compose up -d
```
> **docker-compose 相关命令**
>
> ```shell
> docker container ls  # 查看容器信息，主要看它的名称 NAMES，方便进入容器内部
> docker exec -it nextcloud_app_1 bash  # 进入容易内部，nextcloud_app_1 是容器的名称，上面那个命令查出来的
> exit # 进入容器内部后，要退出的话 输入 exit 即可
> docker ps -a  # 查看所有 docker 容器
> docker ps  # 查看正在运行 docker 容器
> docker stop 容器名称或者ID  # 停止正在运行的容器
> docker rm 容器名称或者ID  #移除容器，移除前先停止容器
> ```



## 进入容器配置登录

`icloudpd-icloudpd_user1-1`为容器名，根据实际情况替换

```shell
#进入容器
sudo docker exec -it icloudpd-icloudpd_user1-1 sh
# 初始化命令
/usr/local/bin/sync-icloud.sh --Initialize
```

以下为进入容器后的操作过程

```sh
saber@raspberrypi5:/www/server/icloudpd $ sudo docker exec -it icloudpd-icloudpd_user1-1 sh
/ # cat /config/icloudpd.conf 
agentid=
albums_with_dates=false
apple_id=yujie.liu6@outlook.com
auth_china=true
authentication_type=MFA
auto_delete=false
bark_device_key=
bark_server=
content_source_url=
convert_heic_to_jpeg=false
debug_logging=false
delete_accompanying=false
delete_after_download=false
delete_notifications=true
dingtalk_token=
directory_permissions=755
discord_id=
discord_token=
download_notifications=true
download_path=
file_permissions=755
folder_structure={:%Y/%m/%d}
gotify_app_token=
gotify_https=
gotify_server_url=
group=group
group_id=1000
icloud_china=true
iyuu_token=
jpeg_path=
jpeg_quality=90
libraries_with_dates=false
media_id_delete=
media_id_download=
media_id_expiration=
media_id_startup=
media_id_warning=
nextcloud_delete=false
nextcloud_password=
nextcloud_upload=false
nextcloud_url=
nextcloud_username=
notification_days=7
notification_type=
photo_album=
photo_library=
photo_size=original
prowl_api_key=
pushover_sound=
pushover_token=
pushover_user=
recent_only=
set_exif_datetime=false
single_pass=false
skip_album=
skip_check=false
skip_download=false
skip_library=
skip_live_photos=false
skip_videos=false
startup_notification=true
synchronisation_delay=0
synchronisation_interval=86400
synology_ignore_path=false
telegram_chat_id=
telegram_polling=true
telegram_server=
telegram_silent_file_notifications=
telegram_token=
touser=
trigger_nextlcoudcli_synchronisation=
until_found=
user_id=1000
webhook_https=false
webhook_id=
webhook_path=/api/webhook/
webhook_port=8123
webhook_server=
wecom_id=
wecom_proxy=
wecom_secret=
/ # /usr/local/bin/sync-icloud.sh --Initialize

2024-01-27 14:31:10 INFO     ***** boredazfcuk/icloudpd container for icloud_photo_downloader v1.0.716 started *****
2024-01-27 14:31:10 INFO     ***** For support, please go here: https://github.com/boredazfcuk/docker-icloudpd *****
2024-01-27 14:31:10 INFO     Alpine Linux 3.19.0
2024-01-27 14:31:10 INFO     Python version: 3.11.6
2024-01-27 14:31:10 INFO     Loading configuration from: /config/icloudpd.conf
2024-01-27 14:31:30 INFO     Apple ID: yujie.liu6@outlook.com
2024-01-27 14:31:30 INFO     Authentication Type: MFA
2024-01-27 14:31:30 INFO     Cookie path: /config/yujieliu6outlookcom
2024-01-27 14:31:30 INFO     Cookie expiry notification period: 7
2024-01-27 14:31:30 INFO     Download destination directory: /home/user/iCloud
2024-01-27 14:31:30 INFO     Folder structure: {:%Y/%m/%d}
2024-01-27 14:31:30 INFO     Synchronisation interval: 86400
2024-01-27 14:31:30 INFO     Synchronisation delay (minutes): 0
2024-01-27 14:31:30 INFO     Set EXIF date/time: false
2024-01-27 14:31:30 INFO     Auto delete: false
2024-01-27 14:31:30 INFO     Delete after download: false
2024-01-27 14:31:30 INFO     Photo size: original
2024-01-27 14:31:30 INFO     Single pass mode: false
2024-01-27 14:31:30 INFO     Skip download check: false
2024-01-27 14:31:30 INFO     Skip live photos: false
2024-01-27 14:31:30 INFO     Number of most recently added photos to download: Download All Photos
2024-01-27 14:31:30 INFO     Downloading photos from: Download All Photos
2024-01-27 14:31:30 INFO     Stop downloading when prexisiting files count is: Download All Photos
2024-01-27 14:31:30 INFO     Live photo size: original
2024-01-27 14:31:30 INFO     Skip videos: false
2024-01-27 14:31:30 INFO     Convert HEIC to JPEG: false
2024-01-27 14:31:30 INFO     Downloading from: icloud.com.cn
2024-01-27 14:31:30 INFO     Authentication domain: cn
2024-01-27 14:31:30 INFO     Ignore Synology extended attribute directories: Disabled
2024-01-27 14:31:32 INFO     Script launch parameters: --Initialize
Enter iCloud password for yujie.liu6@outlook.com: 
Save password in keyring? [y/N]: y

Two-step authentication required. 
Please enter validation code
(string) --> 006568

2024-01-27 14:32:09 INFO     Starting container initialisation
Please enter two-factor authentication code: 505334
2024-01-27 14:32:24 WARNING  Failed to parse response with JSON mimetype
2024-01-27 14:32:31 INFO     Multifactor authentication cookie generated. Sync should now be successful
2024-01-27 14:32:31 INFO     Container initialisation complete
```

## 参考链接

[boredazfcuk](https://github.com/boredazfcuk)/**[docker-icloudpd](https://github.com/boredazfcuk/docker-icloudpd)**

https://github.com/boredazfcuk/docker-icloudpd/issues/309

https://dockerdocs.cn/compose/environment-variables/