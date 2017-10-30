# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = 'ubuntu/trusty64'

  config.vm.provider :virtualbox do |vb|
    vb.customize [
      'modifyvm', :id,
      '--cpuexecutioncap', '90',
      '--memory', '512',
      '--natdnshostresolver1', 'on',
      '--ioapic', 'on',
    ]
  end

  N = 7
  (1..N).each do |machine_id|
    config.vm.define "machine#{machine_id}" do |machine|
      config.vm.box = 'ubuntu/trusty64'
      if machine_id >= 6
        config.vm.box = 'ubuntu/xenial64'
      end
      if machine_id == 4
        config.vm.box = 'centos/7'
      end
      machine.vm.hostname = "machine#{machine_id}"
      machine.vm.network 'private_network', ip: "192.168.99.#{2+machine_id}"

      if machine_id == N
        machine.vm.provision :ansible do |ansible|
          ansible.extra_vars = "vagrant_vars.yml"
          ansible.limit = 'all'
          ansible.playbook = 'vagrant.yml'
          ansible.groups = {
            'master' => ['machine1'],
            'mon' => ['machine2'],
            'procexporter' => ['machine3'],
            'postgres' => ['machine4'],
            'mysql' => ['machine5', 'machine6'],
            'redis' => ['machine7'],
          }
          ansible.verbose = ENV['ANSIBLE_VERBOSE'] ||= "v"
          ansible.tags = ENV['ANSIBLE_TAGS'] ||= "all"
          ansible.raw_arguments = [
            '--become',
            ]
        end
        end
      end
    end
end
