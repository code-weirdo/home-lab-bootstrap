# Intro

Repository for standing up of a multi-master, highly available Raspberry Pi 4 K3s cluster using embedded etcd.

# Raspberry Pi Setup

- Download preinstalled Ubuntu server image for Raspberry Pi 4 - eg. [Ubuntu 22.04](http://cdimage.ubuntu.com/releases/jammy/release/ubuntu-22.04.1-preinstalled-server-arm64+raspi.img.xz)
- Download and install the RPi Imager [here](https://downloads.raspberrypi.org/imager/imager_latest_amd64.deb)
- Write image to SD CARD
  - Choose OS->Use Custom
  - Settings
    - Enable SSH->Use password authentication
    - Set username and password eg. ubuntu/ubuntu as per Ubuntu image
- Boot Raspberry Pi's
- Assign or fix the host IP addresses on DHCP server
- Reboot Raspberry Pi's

# Bootstrapping

The bootstrapping phase uses Ansible to build out the K3s cluster using the method described [here](https://docs.k3s.io/installation/ha-embedded) rather than using existing Ansible playbooks that manually set everything up.

- Make sure you have a file called .secrets in the root and populate it with the secrets needed - use ./secrets/secrets.template as a starting point (.secrets won't be committed to this repo)
- `./cluster bootstrap-ansible` will prompt for a password for the hosts in the cluster and will set up the ssh key - just make sure you have a public key under ~/.ssh/id-rsa.pub
- `./cluster bootstrap-k3s` will install any prerequisites and set up the K3s cluster, finally copying the kubeconfig file to ~/.kube/config_homelab on the local host.
- `mv ~/.kube/config ~/.kube/config.backup && ln -sf ~/.kube/config_homelab ~/.kube/config` will make a simlink to the homelab configuration file. Alternatively just rename it to ~/.kube/config.

At this point you should be able to run `kubectl get nodes` and should list all of the nodes in the cluster

- `./cluster bootstrap-flux` will get Flux set up on the cluster which will take over the management of the cluster from here
