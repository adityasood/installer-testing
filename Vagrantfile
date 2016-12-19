##########################################################################
# Copyright 2016 ThoughtWorks, Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
##########################################################################

# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  boxes = {
    'ubuntu-12.04' => {virtualbox: 'boxcutter/ubuntu1204',  docker: 'ubuntu/precise'},
    'ubuntu-14.04' => {virtualbox: 'boxcutter/ubuntu1404',  docker: 'ubuntu/trusty'},
    'centos-6'     => {virtualbox: 'boxcutter/centos67',    docker: 'centos/centos6'},
    'centos-7'     => {virtualbox: 'boxcutter/centos72',    docker: 'centos/centos7'},
  }

  boxes.each do |name, box_cfg|
    config.vm.define name do |vm_config|
      vm_config.vm.network "private_network", type: "dhcp"

      if name =~ /ubuntu/

        vm_config.vm.provision "shell", inline: "apt-get install -y software-properties-common python-software-properties"
        vm_config.vm.provision "shell", inline: "add-apt-repository ppa:openjdk-r/ppa"
        vm_config.vm.provision "shell", inline: "apt-get update"
        vm_config.vm.provision "shell", inline: "apt-get install -y rake ruby-json openjdk-8-jre unzip git"
        vm_config.vm.provision "shell", inline: "sudo -i GO_VERSION=#{ENV['GO_VERSION']} USE_POSTGRES=#{ENV['USE_POSTGRES'] || 'No'} UPGRADE_VERSIONS_LIST=\"#{ENV['UPGRADE_VERSIONS_LIST'] || ''}\" rake --trace --rakefile /vagrant/provision/Rakefile debian:#{ENV['TEST'] || 'fresh'}"
      elsif name =~ /centos/
        vm_config.vm.provision "shell", inline: "yum makecache"
        vm_config.vm.provision "shell", inline: "yum install -y epel-release centos-release-scl"
        vm_config.vm.provision "shell", inline: "yum install -y java-1.8.0-openjdk unzip git rh-ruby22-rubygem-rake"
        vm_config.vm.provision "shell", inline: "echo 'source /opt/rh/rh-ruby22/enable' > /etc/profile.d/ruby-22.sh"
        vm_config.vm.provision "shell", inline: "sudo -i GO_VERSION=#{ENV['GO_VERSION']} USE_POSTGRES=#{ENV['USE_POSTGRES'] || 'No'} UPGRADE_VERSIONS_LIST=\"#{ENV['UPGRADE_VERSIONS_LIST'] || ''}\" rake --trace --rakefile /vagrant/provision/Rakefile centos:#{ENV['TEST'] || 'fresh'}"
      end


      vm_config.vm.provider :virtualbox do |vb, override|
        override.vm.box = box_cfg[:virtualbox]
        vb.gui    = ENV['GUI'] || false
        vb.memory = ((ENV['MEMORY'] || 4).to_f * 1024).to_i
        vb.cpus   = 4
      end

      vm_config.vm.provider :vmware_fusion do |vm, override|
        override.vm.box = box_cfg[:virtualbox]
        vm.gui    = ENV['GUI'] || false
        vm.memory = ((ENV['MEMORY'] || 4).to_f * 1024).to_i
        vm.cpus   = 4
      end
    end
  end

#   if Vagrant.has_plugin?('vagrant-proxyconf')
#     # use ip 10.0.2.2 for virtualbox
#     config.proxy.http     = 'http://10.0.2.2:3128/'
#     config.proxy.https    = 'http://10.0.2.2:3128/'
#     config.proxy.no_proxy = 'localhost,127.0.0.1,172.16.18.1,172.16.38.21,10.0.2.2'
#   end
#
#   if Vagrant.has_plugin?('vagrant-ohai')
#    config.ohai.primary_nic = "eth1"
#   end

   if Vagrant.has_plugin?('vagrant-cachier')
     config.cache.scope = :box
     config.cache.enable :apt
     config.cache.enable :apt_lists
     config.cache.enable :yum
   end
end
