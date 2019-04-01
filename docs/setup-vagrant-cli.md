# Table of contents

- [1. Giới thiệu Vagrant](#introduce)
- [2. Cài đặt vagrant, virtualbox](#install)
  - [2.1 Cài đặt vagrant](#install_vagrant)
  - [2.2 Cài đặt virtualbox](#install_virtualbox)
- [3. Sử dụng vagrant](#use)
  - [3.1 Các lệnh cơ bản ](#commands)
  - [3.2 Tạo một virtual machine](#create_vm)
  - [3.3 Vagrantfile với nhiều máy ảo](#multi_vms)
    + [3.3.1 Tạo nhiều máy ảo với cấu hình khác nhau](#create_other_vms)
    + [3.3.2 Tạo nhiều máy ảo với cùng cấu hình](#create_vms)
- [4. Cấu hình một số tùy chọn](#config_options)

===========================================================================

## <a name="introduce">1. Giới thiệu Vagrant</a> 

Vagrant là công cụ cho phép tạo và quản lý máy ảo. Nó cung cấp dòng lệnh đơn giản và dễ dàng cho sử dụng và quản lý máy ảo.

### Tại sao sử dụng Vagrant?

- Vagrant cung cấp dòng lệnh đơn giản và dễ dàng cho tạo và quản lý máy ảo

- Triển khai máy ảo nhanh chóng

- Dễ dàng di chuyển máy ảo, thiết lập sang máy khác

- Cho phép chia sẻ máy ảo dùng chung

### Ai có thể sử dụng Vagrant?

- Developers

- Operators

..

## <a name="install">2. Cài đặt vagrant, virtualbox</a>

### <a name="install_vagrant">2.1. Cài đặt vagrant</a>

Download vagrant tại: https://www.vagrantup.com/downloads.html

- Cài đặt vagrant trên CentOS

`sudo rpm -ivh https://releases.hashicorp.com/vagrant/2.1.2/vagrant_2.1.2_x86_64.rpm`

- Cài đặt vagrant trên Ubuntu/Debian

`curl -O https://releases.hashicorp.com/vagrant/2.1.2/vagrant_2.1.2_x86_64.deb && sudo dpkg --install vagrant_2.1.2_x86_64.deb`

### <a name="install_virtualbox">2.2 Cài đặt virtualbox</a>

Vagrant hỗ trợ một hypervisor: VirtualBox, Hyper-V, and Docker. Chúng bài viết này ta sẽ sử dụng Virtualbox để tạo máy ảo.

- Download Virtualbox tại: [virtualbox](https://www.virtualbox.org/wiki/Linux_Downloads)

- Cài đặt Virtualbox trên CentOS

		cd /etc/yum.repos.d
		wget http://download.virtualbox.org/virtualbox/rpm/rhel/virtualbox.repo
		yum install VirtualBox-5.2

- Cài đặt Virtualbox trên Ubuntu-16.04 (Xenial)

`curl -O https://download.virtualbox.org/virtualbox/5.2.16/virtualbox-5.2_5.2.16-123759~Ubuntu~xenial_amd64.deb && sudo dpkg --install virtualbox-5.2_5.2.16-123759~Ubuntu~xenial_amd64.deb`

## <a name="use">3. Sử dụng vagrant</a>

### <a name="commands">3.1 các lệnh cơ bản</a>

```
     box             manages boxes: installation, removal, etc.
     destroy         stops and deletes all traces of the vagrant machine
     global-status   outputs status Vagrant environments for this user
     halt            stops the vagrant machine
     help            shows the help for a subcommand
     init            initializes a new Vagrant environment by creating a Vagrantfile
     login           log in to HashiCorp's Vagrant Cloud
     package         packages a running vagrant environment into a box
     plugin          manages plugins: install, uninstall, update, etc.
     port            displays information about guest port mappings
     powershell      connects to machine via powershell remoting
     provision       provisions the vagrant machine
     push            deploys code in this environment to a configured destination
     rdp             connects to machine via RDP
     reload          restarts vagrant machine, loads new Vagrantfile configuration
     resume          resume a suspended vagrant machine
     snapshot        manages snapshots: saving, restoring, etc.
     ssh             connects to machine via SSH
     ssh-config      outputs OpenSSH valid configuration to connect to the machine
     status          outputs status of the vagrant machine
     suspend         suspends the machine
     up              starts and provisions the vagrant environment
     validate        validates the Vagrantfile
     version         prints current and latest Vagrant version
```

### <a name="create_vm">3.2 Tạo một virtual machine</a>

Tạo virtual machine là Ubuntu-16.04

- Download box ubuntu/xenial64

`vagrant box add ubuntu/xenial64`

- Khởi tạo vagrant

		mkdir /home/ubuntu-xenial && cd /home/ubuntu-xenial
		vagrant init ubuntu/xenial64

- Chỉnh sửa Vagrantfile

```
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  config.vm.network "public_network", ip: "192.168.1.100"
  #Provider-specific configuration: virtualbox
  config.vm.provider "virtualbox" do |vb|
    # Customize the amount of memory on the VM:
    vb.memory = "4096"
  end
end
```

- Khởi tạo và chạy máy ảo

`vagrant up`

Quá trình tạo như sau:

```
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'ubuntu/xenial64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'ubuntu/xenial64' is up to date...
==> default: Setting the name of the VM: ubuntu_default_1531970638587_62317
Vagrant is currently configured to create VirtualBox synced folders with
the `SharedFoldersEnableSymlinksCreate` option enabled. If the Vagrant
guest is not trusted, you may want to disable this option. For more
information on this option, please refer to the VirtualBox manual:

  https://www.virtualbox.org/manual/ch04.html#sharedfolders

This option can be disabled globally with an environment variable:

  VAGRANT_DISABLE_VBOXSYMLINKCREATE=1

or on a per folder basis within the Vagrantfile:

  config.vm.synced_folder '/host/path', '/guest/path', SharedFoldersEnableSymlinksCreate: false
==> default: Clearing any previously set network interfaces...
==> default: Available bridged network interfaces:
1) eno2
2) eno3
3) eno4
4) eno5
5) veth6a08cca
6) enp0s29u1u1u5
7) docker_gwbridge
8) docker0
==> default: When choosing an interface, it is usually the one that is
==> default: being used to connect to the internet.
    default: Which interface should the network bridge to? 1
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
    default: Adapter 2: bridged
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection reset. Retrying...
    default: Warning: Remote connection disconnect. Retrying...
    default: 
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default: 
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default: 
    default: Guest Additions Version: 5.1.34
    default: VirtualBox Version: 4.3
==> default: Configuring and enabling network interfaces...
==> default: Mounting shared folders...
    default: /vagrant => /home/virtualbox/ubuntu
```

- Truy cập máy ảo qua ssh

`vagrant ssh`

vagrant@ubuntu-xenial:~$ 

### <a name="multi_vms">3.3. Vagrantfile với nhiều máy ảo</a>

Mặc định, chúng ta khởi tạo Vagrantfile với 01 máy ảo. Trong điều kiện thực tế, cũng như yêu cầu đặt ra cần tạo nhiều máy ảo đồng thời, Vagrantfile cũng cho phép chúng ta thực hiện được điều đó.

#### <a name="create_other_vms">3.3.1 Tạo nhiều máy ảo với cấu hình khác nhau</a>

Tạo Vagrantfile với nội dung sau để cho khởi 01 máy centos/7 và 01 máy ubuntu/xenial64

```
Vagrant.configure("2") do |config|
  config.vm.define "centos" do |centos|
    centos.vm.box = "centos/7"
    centos.vm.hostname = "centos01"
    centos.vm.network "public_network", ip: "192.168.1.111"
    centos.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
    end
  end
  config.vm.define "ubuntu" do |ubuntu|
    centos.vm.box = "ubuntu/xenial64"
    centos.vm.hostname = "ubuntu01"
    centos.vm.network "public_network", ip: "192.168.1.112"
    centos.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
    end
  end
end
```

Ở tệp cấu hình Vagrantfile trên, ta định nghĩa các biến "centos" và "ubuntu" để khai báo cho từng virtual machine.

- Thực hiện quá trình tạo máy ảo

`vagrant up`

#### <a name="create_vms">3.3.2 Tạo nhiều máy ảo với cùng cấu hình</a>

Chúng ta sẽ kết hợp với vòng lặp trong Vagrantfile để tạo ra nhiều máy với cùng hệ điều hành, một số thông số cấu hình giống nhau.
Ở ví dụ dưới đây, chúng ta sẽ tạo ra 02 máy chủ web server, sử dụng CentOS 7, sử dụng cùng dải mạng và với cùng lượng RAM

```
Vagrant.configure("2") do |config|
  (1..2).each do |i|
    config.vm.define "web#{i}" do |web|
        web.vm.box = "centos/7"
        web.vm.hostname = "web#{i}"
        web.vm.network "public_network", ip: "192.168.1.22#{i}"
        web.vm.provider "virtualbox" do |vb|
          vb.memory = "2048"
        end
    end
  end
end
```

## <a name="config_options">4. Cấu hình một số tùy chọn</a>

- Thay đổi đường dẫn "Default Machine Folder"

```
mkdir -p /home/virtualbox

vboxmanage setproperty machinefolder /home/virtualbox
```

Tham khảo đầy đủ cách sử dụng Vagrant tại: [https://www.vagrantup.com/docs/](https://www.vagrantup.com/docs/)
