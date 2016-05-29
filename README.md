
# LXD Sabayon Linux image builder

This script creates a [Sabayon Linux](https://www.sabayon.org/) image to use with [LXD](https://linuxcontainers.org/lxd/).
Actually i think it can be used to create an lxd image from any docker image, but it's untested.

## Usage

In order to build the latest Alpine image just run the script (must be done
as root):

    sudo ./create_image

You can supply a docker image as source:

    sudo IMAGE="sabayon/builder-amd64" ./create_image

To specify the arch (it will fetch the unpacker for you) of the host which is performing the operation, otherwise will default to amd64

    sudo ARCH="arm" IMAGE="sabayon/armhfp" ./create_image

After the image is built it can be added as an image to LXD as follows:

    lxc image import sabayon-v0.1-x86_64-20160529_100.tar.gz --alias sabayon-v0.1


## License

This script uses the same license as the script it was derived from: LGPL 2.1
