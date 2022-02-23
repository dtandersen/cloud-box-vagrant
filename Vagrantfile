# -*- mode: ruby -*-
# vi: set ft=ruby :
# requires set VAGRANT_EXPERIMENTAL="disks"
Vagrant.configure(2) do |config|
  #config.vm.box = "centos/8"
  config.vm.box = "bento/centos-8"
  #config.vm.box_version = "1902.01"
  config.vm.hostname = "cloud-box"
  config.vm.synced_folder ENV['USERPROFILE']  + "/Google Drive/vagrant-work", "/home/vagrant/work"
  #config.disksize.size = '50GB'
  config.vbguest.auto_update = true
  config.ssh.forward_agent = true

  config.vm.network "private_network", ip: "192.168.50.4", virtualbox__intnet: true
  config.vm.disk :disk, size: "50GB", primary: true

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.name = "Cloud Box"
    vb.memory = 4096
    vb.cpus = 4
  end

  config.vm.provision "shell", env: {"TERRAFORM_VERSION" => "1.1.0", "DOCKER_COMPOSE_VERSION" => "2.2.2"}, inline: <<-SHELL
    yum install -y epel-release wget unzip git curl nano
    ln -s /home/vagrant/work/.aws /home/vagrant/.aws

    # install terraform
    wget -nv https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip -O /tmp/terraform.zip
    unzip /tmp/terraform.zip -d /usr/bin
    rm /tmp/terraform.zip

    # install docker
    yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    yum install -y device-mapper-persistent-data lvm2 docker-ce docker-ce-cli containerd.io
    systemctl enable --now docker
    curl -Ls "https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
    chmod +x /usr/bin/docker-compose
    usermod -aG docker vagrant

    # install aws cli
    dnf install -y python3 python3-pip
    #yum install -y python-pip
    pip3 install awscli

    cat <<EOF > /etc/docker/daemon.json
    {
    "exec-opts": ["native.cgroupdriver=systemd"]
    }
    EOF
    systemctl restart docker

    cat <<EOF > /etc/yum.repos.d/kubernetes.repo
    [kubernetes]
    name=Kubernetes
    baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
    enabled=1
    gpgcheck=1
    repo_gpgcheck=1
    gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
    EOF
    yum install -y kubeadm==1.20.13-0
    yum install -y kubectl-1.20.13-0
    swapoff -a
    kubeadm init
    yum install -y iproute-tc
    mkdir /home/vagrant/.kube
    cp /etc/kubernetes/admin.conf /home/vagrant/.kube/config
    sudo chown -R vagrant ~/.kube
    kubectl --kubeconfig /home/vagrant/.kube/config taint nodes $(hostname) node-role.kubernetes.io/master:NoSchedule-
    yum install -y bash-completion

    cat <<EOF > /etc/profile.d/bash_completion.sh
      # Use bash-completion, if available
      [[ $PS1 && -f /usr/share/bash-completion/bash_completion ]] && \
        . /usr/share/bash-completion/bash_completion
    EOF
    kubectl completion bash > /etc/bash_completion.d/kubectl
    echo 'alias k=kubectl' >> /home/vagrant/.bashrc
    echo 'complete -F __start_kubectl k' >> /home/vagrant/.bashrc
    kubectl apply --kubeconfig /home/vagrant/.kube/config -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
    #yum install -y centos-release-scl
    #yum-config-manager --enable centos-sclo-rh-testing
    #yum install -y rh-python36
  SHELL
end
