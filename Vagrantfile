# -*- mode: ruby -*-
# vi: set ft=ruby :
nodes = {
  :iscsi => {
        :box_name => "centos/7",
        :ip_addr => '192.168.7.153',
        :drdb_ip => '192.168.8.153',
	:disks => {
		:satan3 => {
			:dfile => './sata3.vdi',
			:size => 1000,
			:port => 1
		},
         }
  },
  :pcm1 => {
        :box_name => "centos/7",
        :ip_addr => '192.168.7.150',
        :drdb_ip => '192.168.8.150',
	:disks => {
		:satan1 => {
			:dfile => './sata1.vdi',
			:size => 250,
			:port => 1
		},
         }
  },
  :pcm2 => {
        :box_name => "centos/7",
        :ip_addr => '192.168.7.151',
        :drdb_ip => '192.168.8.151',
	:disks => {
		:satan2 => {
			:dfile => './sata2.vdi',
			:size => 250,
			:port => 1
		},
         }
  },
}

Vagrant.configure(2) do |config|
  nodes.each do |boxname, boxconfig|

      config.vm.define boxname do |box|

          box.vm.box = boxconfig[:box_name]
          box.vm.host_name = boxname.to_s
          box.vm.network "private_network", {ip: boxconfig[:ip_addr], adapter: 2}
          box.vm.network "private_network", {ip: boxconfig[:drdb_ip],
                                            adapter: 3,
                                            virtualbox__intnet: "drdb-net"}
	  box.vm.provider :virtualbox do |vb|
             vb.customize ["modifyvm", :id, "--memory", "512"]
             vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
	     needsController = false
             boxconfig[:disks].each do |dname, dconf|
	       unless File.exist?(dconf[:dfile])
	         vb.customize ['createhd', '--filename', 
                               dconf[:dfile], '--variant', 
                               'Fixed', '--size', dconf[:size]]
                 needsController =  true
               end
             end
             if needsController == true
               vb.customize ["storagectl", :id, "--name", "SATA", "--add", "sata" ]
               boxconfig[:disks].each do |dname, dconf|
                         vb.customize ['storageattach', :id,  '--storagectl', 'SATA', 
                                       '--port', dconf[:port], '--device', 0, 
                                       '--type', 'hdd', '--medium', dconf[:dfile]]
               end
             end
          end

          box.vm.provision "ansible" do |ansible|
            ansible.playbook = "site.yml"
            ansible.become = true
          end
      end
  end
end
