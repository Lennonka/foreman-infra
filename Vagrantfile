# -*- mode: ruby -*-
# vi: set ft=ruby :

CENTOS_8_BOX_URL = "https://cloud.centos.org/centos/8-stream/x86_64/images/CentOS-Stream-Vagrant-8-20220913.0.x86_64.vagrant-libvirt.box"

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.provision "install puppet", type: "shell", inline: <<-SHELL
    . /etc/os-release
    yum -y install epel-release
    yum -y install puppet7-release || yum -y install https://yum.puppetlabs.com/puppet7/puppet7-release-el-${VERSION_ID}.noarch.rpm
    yum -y install puppet-agent
  SHELL

  config.vm.provision "run puppet", type: 'puppet' do |puppet|
    puppet.module_path = ["puppet/modules", "puppet/external_modules"]
    puppet.manifests_path = "vagrant/manifests"
    puppet.synced_folder_type = "rsync"
  end

  config.vm.define "jenkins-master" do |override|
    override.vm.hostname = "jenkins-master"

    override.vm.provider "libvirt" do |libvirt|
      libvirt.memory = "2048"
    end
  end

  config.vm.define "jenkins-node-el7" do |override|
    override.vm.hostname = "jenkins-node-el7"

    override.vm.provider "libvirt" do |libvirt|
      libvirt.memory = "4096"
    end

    # Note that VAGRANT_EXPERIMENTAL=dependency_provisioners must be used for
    # before: all to work.
    override.vm.provision "PUP-10548 SELinux workaround", type: "shell", before: :all, inline: <<-SHELL
      yum -y install centos-release-scl-rh
      yum -y install rh-postgresql12-postgresql-server
    SHELL
  end

  config.vm.define "jenkins-node-el8" do |override|
    override.vm.hostname = "jenkins-node-el8"
    override.vm.box = "centos/stream8"

    override.vm.provider "libvirt" do |libvirt, provider|
      libvirt.memory = "4096"
      provider.vm.box_url = CENTOS_8_BOX_URL
    end
  end

  config.vm.define "jenkins-node-debian10" do |override|
    override.vm.hostname = "jenkins-node-debian10"
    override.vm.box = "debian/buster64"

    override.vm.provider "libvirt" do |libvirt|
      libvirt.memory = "4096"
    end
    override.vm.provision "install puppet", type: "shell", inline: <<-SHELL
      . /etc/os-release
      wget https://apt.puppet.com/puppet7-release-${VERSION_CODENAME}.deb
      apt-get install -y ./puppet7-release-${VERSION_CODENAME}.deb
      apt-get update
      apt-get install -y puppet-agent
    SHELL
  end

  config.vm.define "jenkins-deb-node-debian11" do |override|
    override.vm.hostname = "jenkins-deb-node-debian11"
    override.vm.box = "debian/bullseye64"

    override.vm.provider "libvirt" do |libvirt|
      libvirt.memory = "4096"
    end
    override.vm.provision "install puppet", type: "shell", inline: <<-SHELL
      . /etc/os-release
      wget https://apt.puppet.com/puppet7-release-${VERSION_CODENAME}.deb
      apt-get install -y ./puppet7-release-${VERSION_CODENAME}.deb
      apt-get update
      apt-get install -y puppet-agent
    SHELL
  end

  config.vm.define "web" do |override|
    override.vm.hostname = "web"

    override.vm.provider "libvirt" do |libvirt|
      libvirt.memory = "2048"
    end
  end
end
