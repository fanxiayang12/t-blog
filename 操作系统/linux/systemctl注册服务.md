# 目录

`vim /usr/lib/systemd/system/emdata-spring-emr.service`

# emdata-spring-emr.service
```conf
[Unit]
Description=emdata spring emr Service
After=syslog.target

[Service]
ExecStart=/usr/bin/java -jar /home/emdev/emdata-spring-emr-service/emdata-spring-emr-service-1.0.0.jar --spring.profiles.active=prod
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

# 部署脚本

    redeploy.sh

```bash
# stop
systemctl stop emdata-spring-emr.service

# bak old
cp emdata-spring-emr-service-1.0.0.jar emdata-spring-emr-service-1.0.0.jar.bak

# rm old
rm -rf emdata-spring-emr-service-1.0.0.jar

# replace new
cp tmp/emdata-spring-emr-service-1.0.0.jar emdata-spring-emr-service-1.0.0.jar

# start
systemctl start emdata-spring-emr.service

# logs
journalctl -u emdata-spring-emr.service -f
```
