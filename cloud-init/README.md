# cloud-init
For ease of automation, we need to set-up virtual machine templates from available disk images. I'll get to what cloud-init is later on.

## Download Images
I've been successfully working with cloud-init based images. So, first things first, download a cloud-init based image. I usually get them from:

* [CentOS Download Section](https://wiki.centos.org/Download): scroll down to Cloud/Containers and choose Generic, CentOS 7 and x86\_64 arch (RAW or QCow2).
* [Fedora Cloud Images](https://alt.fedoraproject.org/cloud/): choose Cloud Base compressed raw image.
* [Ubuntu Cloud Images](https://cloud-images.ubuntu.com/): choose desired release and download any qcow image.

## Untar Images
Simply `tar zxf` all the things.

## Upload Images to a Datastore
This is the parte where you need to use either [govc](../go/README.md) with `datastore.upload` or VMWare official vSphere client. Any of these options should be pretty straight forward for any administrator. There's the chance you won't be able to use `datastore.upload` because you don't have enough permissions. Si I recommend using the Windows client.

## Create templates
WORK IN PROGRESS.
