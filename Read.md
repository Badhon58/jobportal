# Frontend ENV

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
{
"metrics-addr": "0.0.0.0:9323"
}
$ systemctl restart docker

http://<ip>:9323/metrics

---

# Install Jenkins and Docker

### jenkins.sh file

```
#!/bin/bash

sudo apt update
sudo apt install fontconfig openjdk-21-jre
java -version

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins -y
```

---

### docker and docker compose file

```
#!/bin/bash

# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl start docker
sudo docker --version

sudo apt-get install docker-compose-plugin

sudo docker compose version
```

sudo usermod -aG docker ubuntu
newgrp docker
usermod -aG docker jenkins

---

### Set up a sonarqube Container

```
docker run -it -d --name sonarqube -p 9000:9000 sonarqube:latest
```

Sonarqube Administrator Security
-> sqa_a108c9062fe77f78c957d946da3c7e2e9a8ab7b2

---

# Jenkins

install
sudo apt install gitleaks

sudo apt-get install wget gnupg
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy

---

sudo apt update
sudo apt install -y libatomic1

withSonarQubeEnv('sonar-scanner') {
// some block
}
