# installfest2016

*This is still work in progress*

Demos for my InstallFest 2016 talk

https://youtu.be/rTIjheXbwE8?list=PLub6xBWO8gV_Mr-UuxrHcfUbuGv5n_N5g

##Preparations

I am using Vagrant, you can too.

### Fedora

Go here https://getfedora.org/en/cloud/download/ and download a box of you choice - libvirt or virtualbox.

```
sudo vagrant box add fedora-cloud-23 <url>
sudo vagrant init fedora-cloud-23
sudo vagrant up
sudo vagrant ssh
```

### FreeBSD

If you are using virtualbox, you are good to go with 

```
sudo vagrant init freebsd/FreeBSD-10.2-RELEASE
sudo vagrant up
sudo vagrant ssh
```

If you are using libvirt, you'll have to mutate the box

```
sudo vagrant plugin install vagrant-mutate
sudo vagrant box add freebsd/FreeBSD-10.2-RELEASE
sudo vagrant mutate freebsd/FreeBSD-10.2-RELEASE libvirt
sudo vagrant init freebsd/FreeBSD-10.2-RELEASE --provider libvirt
sudo vagrant up
sudo vagrant ssh
```

### Get rootfs

#### Fedora

```
curl -O https://kojipkgs.fedoraproject.org//packages/Fedora-Docker-Base/25/20170221.0/images/Fedora-Docker-Base-25-20170221.0.x86_64.tar.xz
tar xJf Fedora-Docker-Base-25-20170221.0.x86_64.tar.xz
mkdir rootfs
cd rootfs
tar xf ../5486f867eebad72e716d0c4ed1c278fbc6324f05315a0594738dc06e35e7f7ca/layer.tar
cd ~
```

#### FreeBSD

```
fetch ftp://ftp.freebsd.org/pub/FreeBSD/releases/amd64/10.1-RELEASE/base.txz
mkdir rootfs
cd rootfs
tar xJf base.txz
cd ~
```
##Demos

### chroot

In Fedora box

```
sudo chroot rootfs/
more /etc/os-release | grep PRETTY
```

### BSD Jails

```
uname -a
sudo vi /etc/jail.conf
sudo jail -c installfest
uname -a
#more /etc/sysctl.conf
```

### LXC

```
sudo dnf -y install lxc lxc-extra lxc-templates
sudo lxc-create -n test -P rootfs/ -t none
sudo lxc-start -n test -- bash
```

### systemd-nspawn

```
sudo systemd-nspawn -D rootfs/ bash
```

### Docker

Everybody knows this, right?;)

### runc

```
sudo dnf -y install runc

runc spec
more config.json
sudo runc start some-container
```
### Namespaces

#### Mount

```
mkdir /tmp/test
sudo unshare -m bash
mount --bind /tmp/test /media/
echo "Hello World" > /media/hello.txt
mount | grep media
more /media/hello.txt
```

In second terminal sshed to the VM try

```
mount | grep media
more /media/hello.txt
more /tmp/test/hello.txt
```

#### UTS

```
sudo unshare -u bash
hostname
sysctl -q -w kernel.hostname="installfest"
```

In second terminal sshed to the VM try

```
hostname
```

#### IPC

```
sudo unshare -i bash
ipcs
ipcmk -M 20
ipcs
```

In second terminal sshed to the VM try

```
ipcs
```

#### PID

```
sudo unshare -p --fork bash -c 'sleep 100000 &
bash'
ps
```

Whaaat? How come it's not 1 and 2, but some crazy number? Because ps uses `/proc`

```
sudo unshare -p --fork --mount-proc bash -c 'sleep 100000 &
bash'
ps aux
```

Better, in second terminal sshed to the VM try

```
ps aux | grep sleep 1000
```

#### Network

```
sudo unshare -n bash
ip a
```

#### User

```
unshare -U -r
echo "Ahoj" > aaa
ls -la aaa
```

In second terminal sshed to the VM try

```
ls -la aaa
```

#### Pivot Root

```
sudo unshare -m -p -i -n -u --fork --mount-proc bash
root=/home/vagrant/rootfs
mkdir $root/.pivot
mount --bind $root $root
pivot_root $root $root/.pivot
cd /
exec bash
rpm -q bash
```

### Cgroups

```
sudo sytemd-cgls
sudo systemd-nspawn --slice=test.slice -D rootfs/ bash
sudo systemd-cgls | grep -A 1 -B 1 machine-rootfs\.scope
```

