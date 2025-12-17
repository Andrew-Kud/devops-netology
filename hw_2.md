Задача 1:

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

---


Задача 2:
1.
```
mkdir ~/src
cd ~/src
```

2.
```
ssh-keygen -t ed25519
```
Enter на все вопросы

3.
```
cat > Vagrantfile << 'EOF'
ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'

ISO = "bento/ubuntu-24.04"
NET = "192.168.56."
DOMAIN = ".netology"
HOST_PREFIX = "server"

servers = [
  {
    :hostname => HOST_PREFIX + "1" + DOMAIN,
    :ip => NET + "11",
    :ssh_host => "20011",
    :ssh_vm => "22",
    :ram => 1024,
    :core => 1
  }
]

Vagrant.configure(2) do |config|
  config.vm.synced_folder ".", "/vagrant", disabled: false
  servers.each do |machine|
    config.vm.define machine[:hostname] do |node|
      node.vm.box = ISO
      node.vm.hostname = machine[:hostname]
      node.vm.network "private_network", ip: machine[:ip]
      node.vm.network :forwarded_port, guest: machine[:ssh_vm], host: machine[:ssh_host]
      node.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--memory", machine[:ram]]
        vb.customize ["modifyvm", :id, "--cpus", machine[:core]]
        vb.name = machine[:hostname]
      end
      node.vm.provision "shell",  inline: <<-SHELL
        export DEBIAN_FRONTEND=noninteractive
        sudo apt-get update
        sudo apt-get install -y ca-certificates curl gnupg
        sudo install -m 0755 -d /etc/apt/keyrings
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
        sudo chmod a+r /etc/apt/keyrings/docker.gpg

        echo \
          "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
          $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
          sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
        
        sudo apt-get update
        sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
      SHELL
    end
  end
end
EOF

```

4.
Не забудь, VT-x и AMD-v должны быть включены.
```
vagrant up
```
```
vagrant status
```

5.
```
vagrant ssh
```
```
docker version && docker compose version
```
```
exit
```

---

Задание 3:
