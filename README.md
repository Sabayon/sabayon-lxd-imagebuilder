
# LXD Sabayon Linux image builder

This script creates a [Sabayon Linux](https://www.sabayon.org/) image to use with [LXD](https://linuxcontainers.org/lxd/).
Actually i think it can be used to create an lxd image from any docker image, but it's untested.

## Usage

In order to see all available options execute:

>  ./sabayon-lxd-imagebuilder -h (or --help) 

In order to build the latest Sabayon (x86_64) image just run the script (must be done
as root):

>   sudo ./sabayon-lxd-imagebuilder

You can supply a docker image as source:

>   sudo IMAGE="sabayon/builder-amd64" ./sabayon-lxd-imagebuilder

To specify the arch of the host (it will fetch the unpacker for you) which is performing the operation, define it with HOST_ARCH otherwise will default to amd64

>   sudo HOST_ARCH="arm" ARCH="armv7l" IMAGE="sabayon/armhfp" ./sabayon-lxd-imagebuilder
    
To specify a different target architecture, use ARCH, defaults to x86_64

>   sudo HOST_ARCH="amd64" ARCH="armv7l" IMAGE="sabayon/armhfp" ./sabayon-lxd-imagebuilder

You can tweak also other variables:

* **RELEASE** to specify a release version (defaults to "16")
* **OS** to specify a operative system (defaults to "sabayon")
* **DISTRO** to specify a distribution (defaults to "Sabayon")
* **LXD_TARGET** to specify target LXD server where import image (default to "local:")

After the image is built it can be added as an image to LXD as follow command or automatically if --import option is set:

>   lxc image import sabayon-v0.1-x86_64-20160529_100.tar.gz --alias sabayon-v0.1

After that image is imported, then for create Sabayon Container unprivileged:

>   lxc launch sabayon-v0.1 sabayon-unpriv-container01

or for privileged container:

>   lxc launch sabayon-v0.1 sabayon-priv-container01 -c security.privileged=true


## LXD Daemon configuration

Sabayon OS is based on Systemd that require some particular option on LXC and LXD modules.

For LXD daemon is needed override lxd.service file with these options:

```bash
$# cat /etc/systemd/system/lxd.service.d/00gentoo.conf 

[Service]
LimitNOFILE=infinity
LimitNPROC=infinity
```

For permit use of CGroup to Systemd service inside container:

```bash
$# cat /etc/lxc/lxc.conf 
lxc.cgroup.use = @all
```

To see all available options of LXC module go to [here](https://github.com/lxc/lxd/blob/master/doc/configuration.md).


## Unprivileged containers

Sabayon systemd package currently has `default-hierarchy=hybrid` as compilation option and this means that on boot
container try to use Cgroup v2 and so it doesn't bootstrap correctly.

After create container it is needed force use of Cgroup V1 version with this option on LXD container config:

(For lxc <= 2.0.x):

```yaml
config:
  raw.lxc: |
    lxc.init_cmd = /sbin/init systemd.legacy_systemd_cgroup_controller=yes
```

(For lxc >= 2.1.x):

```yaml
config:
  raw.lxc: |
    lxc.init.cmd = /sbin/init systemd.legacy_systemd_cgroup_controller=yes
```

For details about this issue see [here](https://github.com/lxc/lxc/issues/1669).

## License

This script uses the same license as the script it was derived from: LGPL 2.1
