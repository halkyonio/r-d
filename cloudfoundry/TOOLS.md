## Common tools

- Install wget, helm, jq, brew, maven, k9s
```bash
sudo yum install wget -y
sudo yum install epel-release -y
sudo yum install jq -y 
sudo yum install maven -y
```

- Deploy helm3
```bash
mkdir temp && cd temp
wget https://get.helm.sh/helm-v3.1.2-linux-amd64.tar.gz
tar -vxf helm-v3.1.2-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin
```
- Install `brew` tool on the linux box
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
sudo yum groupinstall 'Development Tools'
echo 'eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)' >> /home/snowdrop/.bash_profile
eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)
```

- Deploy the `k9s` user tool
```bash
wget https://github.com/derailed/k9s/releases/download/v0.17.7/k9s_Linux_x86_64.tar.gz
tar -vxf k9s_Linux_x86_64.tar.gz
sudo mv k9s /usr/local/bin
```