# We'll mount the Chef::Config[:file_cache_path] so it persists between
# Vagrant VMs
host_cache_path = File.expand_path("../.cache", __FILE__)
guest_cache_path = "/tmp/vagrant-cache"

# ensure the cache path exists
FileUtils.mkdir(host_cache_path) unless File.exist?(host_cache_path)

Vagrant.configure("2") do |config|
  config.berkshelf.enabled = true
  config.omnibus.chef_version = :latest

  config.vm.define :ubuntu1404 do |ubuntu1404|
    ubuntu1404.vm.box      = 'opscode-ubuntu-14.04'
    ubuntu1404.vm.box_url  = 'http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_ubuntu-14.04_chef-provisionerless.box'
    ubuntu1404.vm.hostname = 'mydokku.com'
    ubuntu1404.vm.network :private_network, ip: '192.168.50.10'
  end

  config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--memory", 1024]
  end

  config.vm.provision :chef_solo do |chef|
    chef.provisioning_path = guest_cache_path
    chef.log_level         = :debug

    chef.json = {
      
    }

    chef.run_list = %w{
      recipe[dokku]
    }
#      recipe[docker]
  end


  config.vm.synced_folder "./", "/var/lib/dokku/plugins/mongodb"
  config.vm.synced_folder host_cache_path, guest_cache_path
end
