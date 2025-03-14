# Minimal Ansible Project

A bare-minimum Ansible setup.

## Usage

1. Edit the `inventory` file to add your servers
2. Run the playbook:

```bash
ansible-playbook site.yml
```

## Testing Locally

### Option 1: Test on localhost

1. Update the inventory file:

```
[servers]
localhost ansible_connection=local
```

2. Run the playbook:

```bash
ansible-playbook site.yml
```

### Option 2: Test with Vagrant

1. Install Vagrant and VirtualBox
2. Create a Vagrantfile:

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.network "private_network", ip: "192.168.56.10"
end
```

3. Start the VM:

```bash
vagrant up
```

4. Update the inventory file:

```
[servers]
192.168.56.10 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/default/virtualbox/private_key
```

5. Run the playbook:

```bash
ansible-playbook site.yml
```

### Option 3: Test with Docker

1. Start a test container:

```bash
docker run -d --name ansible-test -p 2222:22 ubuntu:20.04
# Set up SSH and other requirements for the container - this varies
```

2. Update the inventory file:

```
[servers]
localhost ansible_port=2222 ansible_user=root
```

3. Run the playbook:

```bash
ansible-playbook site.yml
``` 