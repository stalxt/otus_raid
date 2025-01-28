
MACHINES = {
  :"ansible-raid" => {
              :box_name => "ubuntu/jammy64",
              :box_version => "1.0.0",
              :cpus => 2,
              :memory => 1024,
              :disks => {
		      :sdd1 => {
		        	:dfile => './disks/sdd1.vmdk',
			        :size => '250', # Megabytes
			        :port => 1
		       },
		       :sdd2 => {
                                 :dfile => './disks/sdd2.vmdk',
                                 :size => '250', # Megabytes
			         :port => 2
		       },
                       :sdd3 => {
                                 :dfile => './disks/sdd3.vmdk',
                                 :size => '250', # Megabytes
                                 :port => 3
                       },
                       :sdd4 => {
                                 :dfile => './disks/sdd4.vmdk',
                                 :size => '250', # Megabytes
                                 :port => 4
                       },
                       :sdd5 => {
                                 :dfile => './disks/sdd5.vmdk',
                                 :size => '250', # Megabytes
                                 :port => 5
                       }

	       }

            }
}
ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'
Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.synced_folder ".", "/vagrant", disabled: true
    if Vagrant.has_plugin?("vagrant-vbguest")
       config.vbguest.auto_update = false
    end
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.box_version = boxconfig[:box_version]
      box.vm.host_name = boxname.to_s
      box.vm.provider "virtualbox" do |v|
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
        needsController = false
        boxconfig[:disks].each do |dname, dconf|
	        unless File.exist?(dconf[:dfile])
		      v.customize ['createhd', '--filename', dconf[:dfile], '--variant', 'Fixed', '--size', dconf[:size]]
                      needsController =  true
                 end
        end   
         if needsController == true
                     v.customize ["storagectl", :id, "--name", "Virtual I/O Device SCSI controller", "--add", "virtio-scsi" ]
                     boxconfig[:disks].each do |dname, dconf|
                         v.customize ['storageattach', :id,  '--storagectl', 'Virtual I/O Device SCSI controller', '--port', dconf[:port], '--device', 0, '--type', 'hdd', '--medium', dconf[:dfile]]
                     end
        
        end        
      end
      box.vm.provision "shell", path: "raid_create.sh"    
    end
  end
end
