# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
  config.vm.box_version = "1902.01"
  config.vm.hostname = "cloud-box"
  config.vm.synced_folder ENV['USERPROFILE']  + "/Google Drive/vagrant-work", "/home/vagrant/work"
  config.disksize.size = '80GB'

  config.ssh.forward_agent = true

  config.vm.network "private_network", ip: "192.168.50.4", virtualbox__intnet: true

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.name = "Cloud Box"
    vb.memory = 4096
    vb.cpus = 4
  end

  config.vm.provision "shell", env: {"TERRAFORM_VERSION" => "0.11.11"}, inline: <<-SHELL
    yum install -y epel-release wget unzip git curl nano

    # install terraform
    wget -nv https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip -O /tmp/terraform.zip
    unzip /tmp/terraform.zip -d /usr/bin
    rm /tmp/terraform.zip

    # install docker
    yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    yum install -y device-mapper-persistent-data lvm2 docker-ce docker-ce-cli containerd.io
    systemctl enable --now docker
    curl -Ls "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
    chmod +x /usr/bin/docker-compose
    usermod -aG docker vagrant

    # install aws cli
    yum install -y python-pip
    pip install awscli
    yum install -y centos-release-scl
    #yum-config-manager --enable centos-sclo-rh-testing
    yum install -y rh-python36
  SHELL
end
