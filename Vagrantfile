VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  #config.vm.provider = 'virtualbox'

  config.vm.define "gitlab" do | p |
    p.vm.box = 'hashicorp/bionic64'
    p.vm.host_name = "gitlab"

      p.vm.provider :vmware_esxi do |res|

        #  REQUIRED!  ESXi hostname/IP
        res.esxi_hostname = '192.168.110.200'
        #  ESXi username
        res.esxi_username = 'root'
        #  IMPORTANT!  Set ESXi password.
        #    1) 'prompt:'
        #    2) 'file:'  or  'file:my_secret_file'
        #    3) 'env:'  or 'env:my_secret_env_var'
        #    4) 'key:'  or  key:~/.ssh/some_ssh_private_key'
        #    5) or esxi.esxi_password = 'my_esxi_password'
        res.esxi_password = 'prompt:'
        #  SSH port.
        res.esxi_hostport = 22
        #  OPTIONAL.  When automatically naming VMs, use this prefix.
        res.guest_name_prefix = 'v-'
        #  OPTIONAL.  Set the guest username login.  The default is 'vagrant'.
        res.guest_username = 'dvovchinnikov'
        #  OPTIONAL.  Memory size override
        res.guest_memsize = '8192'
        #  OPTIONAL.  Virtual CPUs override
        res.guest_numvcpus = '2'
      end

      config.vm.provision "shell", inline: <<-SHELL
      export DEBIAN_FRONTEND=noninteractive
      apt-get update
      # install docker & docker-compose
      apt-get install -y docker.io docker-compose
      # install gitlab: https://about.gitlab.com/install/#ubuntu
      apt-get install -y curl openssh-server ca-certificates tzdata perl
      curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
      sudo EXTERNAL_URL="http://gitlab.localdomain" apt-get install gitlab-ee
      # pull some images in advance
      docker pull gitlab/gitlab-runner:latest
      docker pull sonarsource/sonar-scanner-cli:latest
      docker pull golang:1.17
      docker pull docker:latest
      # set sysctl for sonarqube
      sysctl vm.max_map_count=262144
      # run sonarqube
      cd /vagrant && docker-compose up -d
      # add some records to /etc/hosts
      #echo -e "192.168.56.10\tubuntu-bionic\tubuntu-bionic" >> /etc/hosts
      #echo -e "192.168.56.10\tgitlab.localdomain\tgitlab" >> /etc/hosts
    SHELL
end
