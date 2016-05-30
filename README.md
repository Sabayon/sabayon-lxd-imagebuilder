
# LXD Sabayon Linux image builder

This script creates a [Sabayon Linux](https://www.sabayon.org/) image to use with [LXD](https://linuxcontainers.org/lxd/).
Actually i think it can be used to create an lxd image from any docker image, but it's untested.

## Usage

In order to build the latest Alpine image just run the script (must be done
as root):

    sudo ./create_image

You can supply a docker image as source:

    sudo IMAGE="sabayon/builder-amd64" ./create_image

To specify the arch of the host (it will fetch the unpacker for you) which is performing the operation, define it with HOST_ARCH otherwise will default to amd64

    sudo HOST_ARCH="arm" IMAGE="sabayon/armhfp" ./create_image
    
To specify a different target architecture, use ARCH, defaults to x86_64

    sudo HOST_ARCH="amd64" ARCH="armv7l" IMAGE="sabayon/armhfp" ./create_image

You can tweak also other variables:

* **RELEASE** to specify a release version (defaults to "16")
* **OS** to specify a operative system (defaults to "sabayon")
* **DISTRO** to specify a distribution (defaults to "Sabayon")

After the image is built it can be added as an image to LXD as follows:

    lxc image import sabayon-v0.1-x86_64-20160529_100.tar.gz --alias sabayon-v0.1


## License

This script uses the same license as the script it was derived from: LGPL 2.1
