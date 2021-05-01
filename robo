# run robonomics_auto_ded.sh 1 20 0x1111111111111111111111111111111111111111
# где 1 20 количество нод которые будут созданы и ваш адрес

COUNTER=$1
COUNTER_NUM=$2
Ethereum_addres=$3

fallocate -l 2048M /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile swap swap defaults 0 0' >> /etc/fstab
DOWNLOAD_URL=$(curl -s https://github.com/airalab/robonomics/releases/download/v0.28.2/robonomics-0.28.2-x86_64-unknown-linux-gnu.tar.gz \
         | grep browser_download_url \
         | grep x86_64-unknown-linux-gnu.tar.gz \
         | cut -d '"' -f 4)
curl -L "$DOWNLOAD_URL" --output /tmp/robonomics.gz
tar xf /tmp/robonomics.gz -C /usr/bin

apt update
apt install htop

while [[ $COUNTER -lt $COUNTER_NUM ]]; do
#        echo "robonomics$COUNTER"
        useradd --create-home --shell "/bin/bash" "robonomics$COUNTER"
cat >/etc/systemd/system/robonomics_testNet$COUNTER.service <<EOF
[Unit]
Description=robonomics service
After=network-online.target
Wants=network-online.target
StartLimitIntervalSec=400
StartLimitBurst=20

[Service]
User=robonomics$COUNTER
Group=robonomics$COUNTER
Type=simple
Restart=always
RestartSec=1
LimitNOFILE=49152
Environment="RUST_MIN_STACK=4194304"
ProtectSystem=strict
WorkingDirectory=/home/robonomics$COUNTER
ExecStart=/usr/bin/robonomics --validator --collator-eth-account "$Ethereum_addres" -- --in-peers 1000 --out-peers 1000
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

systemctl enable robonomics_testNet$COUNTER.service
systemctl start robonomics_testNet$COUNTER.service

let COUNTER=COUNTER+1
done
