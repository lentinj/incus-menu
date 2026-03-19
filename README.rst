incus-menu: Manage Incus containers
===================================

Simple front-end to manage common tasks for incus containers.

Setting up new base containers
------------------------------

Generally I create ``base-(distro)`` containers first, with a relevant user and
some apt sources setup, then clone these for actual development boxes.

Create a new base image using incus images::

    incus image list images:
    incus launch images:debian/13 base-d13

Then start, attach and do some basic setup::

    echo "APT { Install-Recommends "false"; };" > /etc/apt/apt.conf.d/no-recommends
    apt update && apt upgrade
    apt install build-essential git openssh-server screen dialog3 curl wget unzip gnupg sudo util-linux sed extrepo

Add some external repostitories, e.g.

    extrepo enable docker-ce r-project nodesource yarn
