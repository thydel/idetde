# Reference of howto we used

- [Installing Maya 2020 on Ubuntu][]
- [How to Install Autodesk Maya 2020 on Ubuntu 20.04][]

[Installing Maya 2020 on Ubuntu]:
	https://knowledge.autodesk.com/support/maya/learn-explore/caas/simplecontent/content/installing-maya-2020-ubuntu.html
	"knowledge.autodesk.com"

[How to Install Autodesk Maya 2020 on Ubuntu 20.04]:
	https://blog.eldernode.com/install-autodesk-maya-2020-on-ubuntu-20-04/
	"blog.eldernode.com"

# Try the official one (with changes for debian)

```bash
sudo apt update
sudo apt-get install alien dpkg-dev debhelper build-essential zlib1g-dev
sudo apt -y install software-properties-common dirmngr apt-transport-https lsb-release ca-certificates
```

Get and extract tar file

```bash
mkdir maya; cd maya
wget http://edutrial.autodesk.com/NET17SWDLD/2020/MAYA/ESD/Autodesk_Maya_2020_ML_Linux_64bit.tgz
tar zxvf Autodesk_Maya_2020_ML_Linux_64bit.tgz
```

Make a simple Makefile in `maya` to help convert all rpm

```Makefile
%.deb: %.rpm; alien -vc $<

rpms := $(wildcard *.rpm)
debs := $(rpms:%.rpm=%.deb)

debs: $(debs)
```

Using, e.g.

```bash
cd maya
cat > Makefile <<'!'
%.deb: %.rpm; alien -vc $<

rpms := $(wildcard *.rpm)
debs := $(rpms:%.rpm=%.deb)

debs: $(debs)
!
```

Convert rpm

```bash
cd maya
make # a better alien -vc *.rpm
```

Note that the Makefile is not fully working because the basename of
the derived `deb` name is not identical to the basename of the
original `rmp`

# Try to install required package that may be available in debian-10

```bash
apt install libtbb-dev libtiff5-dev libssl-dev  libssl1.1 gcc libjpeg62 libcurl4 # missing libpng12-dev
apt install libaudiofile-dev # missing libgstreamer-plugins-base0.10-0
apt install libglw1-mesa libglw1-mesa-dev mesa-utils
apt install xfonts-100dpi xfonts-75dpi ttf-mscorefonts-installer fonts-liberation
apt install tcsh libfam0 libfam-dev xfstt
```

# Install special missing one

```bash
cd /tmp
wget http://launchpadlibrarian.net/183708483/libxp6_1.0.2-2_amd64.deb
sudo dpkg -i libxp6_1.0.2-2_amd64.deb
```

# Try to use ubuntu-16 libs

```console
root@tdelt4:/etc/apt# apt update
Hit:1 http://security.debian.org/debian-security buster/updates InRelease
Ign:2 http://dl.google.com/linux/chrome-remote-desktop/deb stable InRelease
Hit:3 http://ftp.fr.debian.org/debian buster InRelease
Hit:4 http://dl.google.com/linux/chrome/deb stable InRelease
Hit:5 http://ftp.fr.debian.org/debian buster-updates InRelease
Hit:6 http://dl.google.com/linux/chrome-remote-desktop/deb stable Release
Get:7 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]
Err:7 http://archive.ubuntu.com/ubuntu xenial InRelease
  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 40976EAF437D05B5 NO_PUBKEY 3B4FE6\
ACC0B21F32
Reading package lists... Done
W: GPG error: http://archive.ubuntu.com/ubuntu xenial InRelease: The following signatures couldn't be verified because the public ke\
y is not available: NO_PUBKEY 40976EAF437D05B5 NO_PUBKEY 3B4FE6ACC0B21F32
E: The repository 'http://archive.ubuntu.com/ubuntu xenial InRelease' is not signed.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
```

Get missing keys

```console
root@tdelt4:/etc/apt# apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 40976EAF437D05B5
Executing: /var/tmp/apt-key-gpghome.vvtGYjwt2L/gpg.1.sh --keyserver keyserver.ubuntu.com --recv-keys 40976EAF437D05B5
gpg: key 40976EAF437D05B5: public key "Ubuntu Archive Automatic Signing Key <ftpmaster@ubuntu.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
root@tdelt4:/etc/apt# apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 3B4FE6ACC0B21F32
Executing: /var/tmp/apt-key-gpghome.tH2AhJ0hkr/gpg.1.sh --keyserver keyserver.ubuntu.com --recv-keys 3B4FE6ACC0B21F32
gpg: key 3B4FE6ACC0B21F32: public key "Ubuntu Archive Automatic Signing Key (2012) <ftpmaster@ubuntu.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```

