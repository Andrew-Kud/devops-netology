1.
```
sudo apt update && sudo apt upgrade -y
```

2.
```
sudo apt install -y virtualbox linux-headers-$(uname -r) dkms
```

3.
```
wget https://hashicorp-releases.yandexcloud.net/vagrant/2.4.9/vagrant_2.4.9-1_amd64.deb
```

4.
```
sudo apt install ./vagrant_2.4.9-1_amd64.deb
```

5.
```
sudo apt install ./vagrant_2.4.9-1_amd64.deb
```
```
vagrant --version
```

6.
```
wget https://hashicorp-releases.yandexcloud.net/packer/1.14.3/packer_1.14.3_linux_amd64.zip
```
```
unzip packer_1.14.3_linux_amd64.zip
```
```
sudo cp packer /usr/bin/packer
sudo chmod +x /usr/bin/packer
```
```
packer --version
```

7.
```
curl -sSL https://storage.yandexcloud.net/yandexcloud-yc/install.sh | bash
```
```
sudo cp ./yandex-cloud/bin/yc /usr/bin/
sudo chmod +x /usr/bin/yc
```
```
yc init
```
```
https://yandex.cloud/ru/docs/iam/concepts/authorization/oauth-token
1
y
1
```
```
rm -rf ~/yandex-cloud/
```

