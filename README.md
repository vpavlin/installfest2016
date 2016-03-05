# installfest2016

*This is still work in progress*

Demos for my InstallFest 2016 talk

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

## Fedora

```
curl -O https://kojipkgs.fedoraproject.org//work/tasks/5140/13225140/Fedora-Docker-Base-24-20160304.0.x86_64.tar.xz
tar xJf Fedora-Docker-Base-24-20160304.0.x86_64.tar.xz
mkdir rootfs
cd rootfs
tar xJf ../6ade26bbe019a71c1fa18976f6a2c9e398c8a289e053eb9ed55b0044f064ffde/layer.tar.xz
```

## FreeBSD

```
fetch ftp://ftp.freebsd.org/pub/FreeBSD/releases/amd64/10.1-RELEASE/base.txz
mkdir rootfs
cd rootfs
tar xJf base.txz
```

## chroot

In Fedora box

```
sudo chroot rootfs/
more /etc/os-release | grep PRETTY
```

## BSD Jails

## LXC

## systemd-nspawn

## Docker

## runc
