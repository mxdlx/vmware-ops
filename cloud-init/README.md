# cloud-init
For ease of automation, we need to set-up virtual machine templates from available disk images. I'll get to what cloud-init is later on.

## 1) Download Images
I've been successfully working with cloud-init based images. So, first things first, download a cloud-init based image. I usually get them from:

* [CentOS Download Section](https://wiki.centos.org/Download): scroll down to Cloud/Containers and choose Generic, CentOS 7 and x86\_64 arch (RAW or QCow2).
* [Fedora Cloud Images](https://alt.fedoraproject.org/cloud/): choose Cloud Base compressed raw image.
* [Ubuntu Cloud Images](https://cloud-images.ubuntu.com/): choose desired release and download any qcow image.

## 2) Untar Images
Simply `tar zxf` all the things.

## 3) Upload Images to a Datastore
This is the part where you need to use either [govc](../go/README.md) with `datastore.upload` or VMWare official vSphere client. Any of these options should be pretty straight forward for any administrator. There's the chance you won't be able to use `datastore.upload` because you don't have enough permissions. So I recommend using the Windows client.

## 4) Create templates
Next step is creating templates from images. Once again this is something that any administrator should easily do. There are tons of videos on how to do this, like [this one](https://www.youtube.com/watch?v=1y9KqzhQetQ).

## 5) Cloud Init ISO
Cloud Init is a tool that handles the initialization of "cloud" instances. This means that an administrator must set-up YAML configuration files, pack them as an ISO image and load the image in the VM. After Operating System start-up, cloud-init is executed setting up desired system settings like hostname or network configuration. Official documentation can be found [here](http://cloudinit.readthedocs.io/en/latest/) for its latest version. Notice that an ISO image is needed per VM.

**IMPORTANT**: Cloud Init can be pretty buggy and sometimes configuration is a matter of trial and error. Cloud images can be booted to a fallback mode given that VM is unreachable (i.e.: network configuration error). So, VMWare virtual console is the only way of inspecting what went wrong.

#### Quick Start Guide

(**IMPORTANT: next example works with CentOS Cloud 201802**)
A basic example needs two files: `meta-data` and `user-data`. Let's start with meta-data:

```bash
instance-id: server1
local-hostname: server1
network-interfaces: |
  iface ens160 inet static
  address 10.63.1.131
  network 10.63.1.0
  netmask 255.255.255.0
  broadcast 10.63.1.255
  gateway 10.63.1.1
```

**Notes**
* Both `meta-data` and `user-data` files have no file extension.
* Notice that network interface naming scheme must be known.
* I haven't really needed instance-id. If changes are made to `meta-data` or `user-data` the administrator is supposed to change this value so cloud-init can tell that it has been executed before.

Now, `user-data`:

```bash
#cloud-config
ssh_pwauth: False
ssh_authorized_keys:
  - <YOUR-PUBLIC-KEY-STRING-HERE>
  - <ANOTHER-KEY-STRING-HERE>
chpasswd:
  list: |
     root:<ROOT-PASSWORD-HERE>
     centos:<DEFAULT-USER-PASSWORD-HERE>
  expire: False

runcmd:
  - cat /etc/sysconfig/network-scripts/ifcfg-ens160 | grep GATEWAY; ret=$?; if [ $ret -eq 1 ]; then echo 'GATEWAY=10.63.1.1' >> /etc/sysconfig/network-scripts/ifcfg-ens160; fi
```

**Notes**
* Cloud images set-up default users. In CentOS, default username is centos. In Ubuntu, default username is ubuntu. An so on.
* First line, `#cloud-config` is mandatory.
* **ssh\_pwauth**: by default, cloud images disable password authentication. I decided to enable it just for testing purposes.
* **ssh\_authorized\_keys**: this is a YAML list of public SSH keys to be set for the default user.
* **chpasswd**: used to set password for users, notice that default user centos username is used.
* **runcmd**: this is another list with commands to be run once the system is fully initialized. A lot of manipulation can be done here. In this example I ran into a cloud-init bug which ignored network gateway configuration. So I ended up doing that nasty one-liner.

#### Pack it
If you're using any GNU/Linux distribution, then geniso can pack this files:

```bash
$ genisoimage -output cloud-init-server1.iso -volid cidata -joliet -rock user-data meta-data
```

## 6) Upload ISO to Datastore
Same as Step 3, we need these ISO images uploaded to a preferred datastore.
