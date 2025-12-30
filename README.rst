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
    apt install build-essential git openssh-server screen dialog ne curl

Add some external repostitories, e.g.

Node::

    DISTRO=bullseye
    VERSION=node_16.x
    KEYRING=/usr/share/keyrings/nodesource.gpg
    curl -fsSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | gpg --dearmor -o "$KEYRING"
    cat <<EOF > /etc/apt/sources.list.d/nodesource.list
    deb [signed-by=$KEYRING] https://deb.nodesource.com/$VERSION $DISTRO main
    deb-src [signed-by=$KEYRING] https://deb.nodesource.com/$VERSION $DISTRO main
    EOF

Yarn::

    KEYRING=/usr/share/keyrings/yarn.gpg
    curl -fsSL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor -o "$KEYRING"
    cat <<EOF > /etc/apt/sources.list.d/yarn.list
    deb [signed-by=$KEYRING] https://dl.yarnpkg.com/debian/ stable main
    EOF

CRAN::

    DISTRO=bullseye
    KEYRING=/usr/share/keyrings/cran.gpg
    # NB: CRAN instructions currently not updated, pulled key out of old installation
    # https://cloud.r-project.org/bin/linux/debian/
    curl https://raw.githubusercontent.com/lentinj/incus-menu/master/cran.asc | gpg --dearmor -o "$KEYRING"
    cat <<EOF > /etc/apt/sources.list.d/cran.list
    deb [signed-by=$KEYRING] http://cloud.r-project.org/bin/linux/debian ${DISTRO}-cran40/
    EOF

MySQL::

    apt install lsb-release
    wget https://dev.mysql.com/get/mysql-apt-config_0.8.25-1_all.deb
    dpkg -i mysql-apt-config_0.8.25-1_all.deb
