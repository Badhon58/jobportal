Frontend ENV

```
NEXT_PUBLIC_API_URL=http://13.126.159.42:5000/api
```

backend env:

```
PORT=5000
DBURL=mongodb://admin:badhon@13.126.159.42:27017/jobportal?authSource=admin
SECRET_KEY=BadhonBiswas

```

vim /etc/docker/daemon.json
add this file ->
"metrics-addr": "0.0.0.0:9323"
$ systemctl restart docker

http://<ip>:9323/metrics