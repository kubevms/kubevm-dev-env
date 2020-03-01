# Introduction
The project allows creating a test environment for the kubevm project. The test environment includes a VM running kubernetes (minikube) and a VM running OpenStack (packstack).

The vagrant file sets up the two VMs, creates a test project in OpenStack and finally configures and installes the kubevm operator in openstack; the operator gets configured to properly manage the test project created in OpenStack.

# Setting up the environment
As a prerequisite it is necessary a Linux or MacOS box with [Vagrant](https://www.vagrantup.com/) and [VirtualBox 6.1](https://www.virtualbox.org/) installed. VirtualBox 6.1 is required because it supports nested virtualization on both AMD and Intel CPUs.

Vagrant requires also three plug-ins:
*  *(Required)* `hostmanager` to update /etc/hosts. Can be installed with the command: `vagrant plugin install vagrant-hostmanager`;
*  *(Required)* `vagrant-disksize` to modify the size of the packstack VM virtual disk. Can be installed with the command: `vagrant plugin install vagrant-disksize`;
*  *(Optional)* `proxyconf` to properly configure the proxy in the spawn virtual machines (must be installed **if and only if** the environment requires proxy traversal to connect to internet). Can be installed with the command: `vagrant plugin install vagrant-proxyconf`. The vagrant file assumes the proxy is installed on the host machine and listens on port 3128 (for example a cntlm proxy).

After cloning the repository it is enough to enter the repository folder and let vagrant povision the machines:

```bash
cd kubevm-dev-env
git submodule update --init # To clone the kubevm submodule
vagrant up
```

Vagrant creates two virtual machines:
*  Minikube
*  Packstack

The automation configures an OpenSTack test user, a project and a routed network. It also builds the operator, configures Kubernetes with secrets to access the OpenStack tenant and deploys the operator in kubernetes.

By default the operator should be running in the `test-namespace` namespace. You can check its health using:

```bash
kubectl get po -n test-namespace
```

Logging into [OpenStack](http://packstack.kubevm-dev.com) using the default credentials `test-user` and `passw0rd` would allow to monitor the creation and destruction of VMs.