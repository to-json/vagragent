# -*- mode: ruby -*-
# vi: set ft=ruby :

require "yaml"

config_file = File.join(File.dirname(__FILE__), "config.yml")
box_config  = YAML.load_file(config_file)

Vagrant.configure("2") do |config|
  config.vm.box = "cloud-image/debian-13"

  config.vm.provider :qemu do |qemu|
    qemu.machine = "virt,accel=hvf,highmem=off"
    qemu.cpu     = "cortex-a57"
    qemu.smp     = "2"
    qemu.memory  = "2G"
  end

  # Mount host repos directory into the guest
  if box_config.dig("workspace", "repos_dir")
    host_repos = File.expand_path(box_config["workspace"]["repos_dir"])
    guest_mount = box_config.dig("workspace", "guest_mount") || "/home/vagrant/repos"
    if File.directory?(host_repos)
      config.vm.synced_folder host_repos, guest_mount
    end
  end

  # Optionally mount host nvim config into the guest
  if box_config.dig("neovim", "sync_host_config")
    host_nvim = File.expand_path("~/.config/nvim")
    if File.directory?(host_nvim)
      config.vm.synced_folder host_nvim, "/home/vagrant/.config/nvim"
    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook   = "ansible/playbook.yml"
    ansible.extra_vars = { box_config: box_config }
  end
end
