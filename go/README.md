# Go
First of all, [Go](https://golang.org/) needs to be installed.
Go needs a directory to layouy its files, refered with its environment variable $GOPATH. A common practice is to set it up at home: ~/go.

## RHEL/CentOS/Fedora

```bash
# YUM
$ mkdir ~/go && yum install golang
```

```bash
# DNF
$ mkdir ~/go && dnf install golang
```

Then export $GOPATH in your shell rc script.

```bash
# .bashrc | .zshrc

export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
```

## [govc](https://github.com/vmware/govmomi/tree/master/govc)
Govc is a vSphere CLI built on top of [govmomi](https://github.com/vmware/govmomi) and the one tool that's going to spare some of the pain brought by vSphere Web Client.

Its installation is as simple as:

```bash
$ go get -u github.com/vmware/govmomi/govc
```

But it needs some environment variables set to work as a CLI, later on this variables will be set by Ansible, but for testing purposes you should set these at least:
```bash
# .bashrc | .zshrc
export GOVC_URL='<vSphere server IPv4 address>'
export GOVC_USERNAME='<vSphere Username>'
export GOVC_PASSWORD='<vSphere Password>'
# Do not check server certificate
export GOVC_INSECURE=1
export GOVC_PERSIST_SESSION='false'
# It's convenient to set a default datastore if you want to test govc
export GOVC_DATASTORE='<datastore name (name only, no brackets)>'
```

If everything is working properly, you can test its [USAGE](https://github.com/vmware/govmomi/blob/master/govc/USAGE.md):

```bash
$ govc about
Name:         VMware vCenter Server
Vendor:       VMware, Inc.
Version:      5.5.0
Build:        4180647
OS type:      win32-x64
API type:     VirtualCenter
API version:  5.5
Product ID:   vpx
UUID:         REDACTED
```
