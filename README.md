# gorilla-timeoff
Automated deployment of timeoff-management app on cloud

## Firsts Approach -  Shell script:
Requirements: CentOS7 instance in GCP.
1. ssh into your CentOS7 instance
2. `sudo -i`
3. copy and paste the following:
````
cat > gorilla-timeoff-script.sh<<EOF
# ===
# Install nodejs:
curl -sL https://rpm.nodesource.com/setup_12.x | bash -
yum install -y nodejs
#
# ===
# Install git:
yum install -y git
#
# ===
# Clone repo:
cd /opt
git clone https://github.com/timeoff-management/application.git timeoff-management
cd timeoff-management/
npm install -g --unsafe-perm
# 
# ===
# Allow traffic on port 3000
firewall-cmd --permanent --zone=public --add-port=3000/tcp
systemctl restart firewalld
sleep 3
#
# ===
# Start timeoff.management
npm start
EOF
````
4. Run `bash gorilla-timeoff-script.sh`
5. Setupt VPC Network firewall rule to allow incoming traffic on port 3000
6. Use a web browser to access the app using your public IP `http://<<public-ip>>:3000/`

## Second Approach -  Docker:
Requirements: CentOS7 instance in GCP.
1. ssh into your CentOS7 instance
2. `sudo -i`
3. copy and paste the following:
````
cat > gorilla-timeoff-docker.sh<<EOF
# ===
# Install docker:
yum install -y yum-utils
yum-config-manager --add-repo http://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce docker-ce-cli containerd.io
systemctl start docker
systemctl enable docker
# ===
# Create Docker Image
mkdir andres-timeoff
cd andres-timeoff
cat > Dockerfile<<EOF
FROM centos:centos7

RUN curl -sL https://rpm.nodesource.com/setup_12.x | bash -
RUN yum install -y nodejs git

WORKDIR /opt

RUN git clone https://github.com/timeoff-management/application.git timeoff-management

WORKDIR /opt/timeoff-management/

EXPOSE 3000

RUN npm install

ENTRYPOINT npm start
EOF
docker build -t timeoff:andres .
# 
# ===
# Allow traffic on port 3000
firewall-cmd --permanent --zone=public --add-port=3000/tcp
systemctl restart firewalld
sleep 3
# 
# ===
# Run docker container with timeoff:andres image
docker run -p 3000:3000 timeoff:andres
EOF
````
4. Run `bash gorilla-timeoff-docker.sh`
5. Setupt VPC Network firewall rule to allow incoming traffic on port 3000
6. Use a web browser to access the app using your public IP `http://<<public-ip>>:3000/`
