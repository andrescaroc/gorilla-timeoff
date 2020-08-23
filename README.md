# gorilla-timeoff
Automated deployment of timeoff-management app on cloud

## Firsts Approach -  Shell script:
Requirements: CentOS7 instance in GCP..
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
4. Setupt VPC Network firewall rule to allow incoming traffic on port 3000
5. Use a web browser to access the app using your public IP `http://<<public-ip>>:3000/`
