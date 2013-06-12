Vagrant::Config.run do |config|

  # The box and its URL
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  # Using hostonly network. You can change this if it doesn't fit your environment
  # (no reason it shouldn't).
  config.vm.network :hostonly, "10.0.0.100"


  # Seems to fix [issue #455](https://github.com/mitchellh/vagrant/issues/455)
  config.vm.customize ["modifyvm", :id, "--rtcuseutc", "on"]
  config.ssh.max_tries = 10

  # Enables NFS shared folders to improve performance 
  config.vm.share_folder("v-root", "code", "../", :nfs => true)

  config.vm.forward_port 3000, 3000 #PDP service port in DEV mode

  # Enables provisioning with chef solo.
  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = ["./cookbooks", "./cookbooks_local"]
    #chef.roles_path = "./roles"
    #chef.data_bags_path = "./data_bags"

    dna = JSON.parse(File.read(File.join(File.dirname(__FILE__), 'chef_dnas', 'lucid32-ruby-1.9.3-p194-pg9.1-mongodb.json')))
    dna.delete("run_list").each do |run_item|
      regexp = %r{(role|recipe)\[([\w:-]+)\]}
      eval "chef.add_#{run_item[regexp, 1]} '#{run_item[regexp, 2]}'"
    end

    chef.json = dna
  end
end
