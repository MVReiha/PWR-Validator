# PWR-Validator
[PWR Chain](https://pwrlabs.io) is the first true Layer 0 blockchain, designed for unmatched scalability and seamless integration. Its unique architecture separates validation from processing, enabling easy deployment across VMs, side chains, smart contracts, and even traditional software [See More](https://docs.pwrlabs.io).

## Requirements
> [!IMPORTANT]
> Minimum hardware specification and other required condition 
> * CPU: 1 vCPU
> * Memory: 1 GB RAM
> * Disk: 50 GB HDD or higher
> * Open TCP Ports: 8231, 8085
> * Open UDP Port: 7621
> * You must have validator role
> * You must have 100k PWR balances

## 1. Install Dependecies Download and install  java JDK 23 : 
```
wget -O javalts.tar.gz https://download.oracle.com/java/23/latest/jdk-23_linux-${arch}_bin.tar.gz  &&
sudo mkdir /usr/local/java &&
sudo mv javalts.tar.gz /usr/local/java &&
cd /usr/local/java &&
sudo tar zxvf javalts.tar.gz &&
sudo rm -rf javalts.tar.gz &&
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/local/java/jdk-23.0.1/bin/java" 1 &&
sudo apt update -y && sudo apt upgrade -y && sudo apt install java-common -y
```

## If you have installed java below version 23 and you want to use java 23 you can uninstall the previous java : 
1 Remove all package related to java 
```
dpkg-query -W -f='${binary:Package}\n' | grep -E -e '^(ia32-)?(sun|oracle)-java' -e '^openjdk-' -e '^icedtea' -e '^(default|gcj)-j(re|dk)' -e '^gcj-(.*)-j(re|dk)' -e '^java-common' | xargs sudo apt-get -y remove;
```
2 Remove config file and etc 
```
sudo apt-get -y autoremove &&
sudo bash -c 'ls -d /home/*/.java' | xargs sudo rm -rf &&
sudo rm -rf /usr/lib/jvm/* &&
sudo rm -rf /usr/local/java &&
sudo update-alternatives --remove-all java  &&
apt purge -y java-common 
```

## 2. Install PWR Validator 
1. Install the validator node software and config file :
```
latest_version=$(curl -s https://api.github.com/repos/pwrlabs/PWR-Validator/releases/latest | grep -Po '"tag_name": "\K.*?(?=")') && \
wget "https://github.com/pwrlabs/PWR-Validator/releases/download/$latest_version/validator.jar" && \
wget https://github.com/pwrlabs/PWR-Validator/raw/refs/heads/main/config.json
```
2. enable port : 
```
sudo ufw allow 8231/tcp && \
sudo ufw allow 8085/tcp && \
sudo ufw allow 7621/udp && \
sudo iptables -A INPUT -p tcp --dport 8085 -j ACCEPT && \
sudo iptables -A INPUT -p tcp --dport 8231 -j ACCEPT && \
sudo iptables -A INPUT -p udp --dport 7621 -j ACCEPT
```
3. run with Systemctl :
```
sudo tee /etc/systemd/system/pwr.service > /dev/null <<EOF
[Unit]
Description=PWR node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME
ExecStart=java -jar validator.jar password IP_ADDRESS --compression-level 0
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable pwr
sudo systemctl restart pwr && sudo journalctl -u pwr -f
```
4. Check Logs 
```
sudo journalctl -u pwr -f
```
5. Get Your Private Key Wallet dan Address : 
```
java -jar validator.jar get-private-key password
```
```
curl -sS http://83.171.xxx.92:8085/address/
```
change 83.171.xxx with your ip 

6. 1 click if upgrade versi :
```
sudo systemctl stop pwr.service && \
sudo rm -rf validator.jar config.json blocks rocksdb && \
latest_version=$(curl -s https://api.github.com/repos/pwrlabs/PWR-Validator/releases/latest | grep -Po '"tag_name": "\K.*?(?=")') && \
wget "https://github.com/pwrlabs/PWR-Validator/releases/download/$latest_version/validator.jar" && \
wget https://github.com/pwrlabs/PWR-Validator/raw/refs/heads/main/config.json && \
sudo ufw allow 8231/tcp && \
sudo ufw allow 8085/tcp && \
sudo ufw allow 7621/udp && \
sudo iptables -A INPUT -p tcp --dport 8085 -j ACCEPT && \
sudo iptables -A INPUT -p tcp --dport 8231 -j ACCEPT && \
sudo iptables -A INPUT -p udp --dport 7621 -j ACCEPT && \
sudo ufw reload && \
sudo pkill -f java && \
sudo systemctl restart pwr && \
sudo journalctl -u pwr -f
```

7. Delete Node : 
```
sudo systemctl stop pwr.service
sudo systemctl disable pwr.service
sudo rm /etc/systemd/system/pwr.service
sudo systemctl daemon-reload
sudo systemctl status pwr.service
sudo journalctl -u pwr.service -f
```

## Join Discord : https://discord.gg/afmNAdVn
## Expoler : https://explorer.pwrlabs.io/



