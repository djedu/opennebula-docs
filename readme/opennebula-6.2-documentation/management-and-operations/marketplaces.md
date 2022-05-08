# Marketplaces

OpenNebula Marketplaces provide a simple way to integrate your cloud with popular application/image providers. Think of them as external datastores.

A Marketplace can be:

* **Public**: accessible universally by all OpenNebula installations.
* **Private**: local within an organization and specific for a single OpenNebula (a single zone) or shared by a federation (a collection of zones). If you are interested in setting up your own private Marketplace, please follow this guide.

A Marketplace stores Marketplace Appliances. A MarketPlace Appliance includes one or more Images and, possibly, some associated metadata like VM Templates or OpenNebula Multi-VM service definitions.

### Public Marketplaces[¶](broken-reference)

OpenNebula will configure by default the following Marketplaces in your installation:

| Marketplace Name         | Description                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------- |
| OpenNebula Public        | The official public [OpenNebula Systems Marketplace](http://marketplace.opennebula.systems) |
| Linux Containers         | The public LXD/LXC [image repository](https://images.linuxcontainers.org)                   |
| Turnkey Linux Containers | The Turnkey Linux [image repository](https://www.turnkeylinux.org)                          |
| DockerHub                | The Docker Hub [image repository](https://hub.docker.com)                                   |

Important

The OpenNebula front-end needs access to the Internet to use the public Marketplaces.

You can list the marketplaces configured in OpenNebula with `onemarket list`. The output for the default installation of OpenNebula will look similar to:

```
$ onemarket list
ID NAME                                                            SIZE AVAIL   APPS MAD     ZONE
3  DockerHub                                                         0M -        164 dockerh    0
2  TurnKey Linux Containers                                          0M -        228 turnkey    0
1  Linux Containers                                                  0M -         24 linuxco    0
0  OpenNebula Public                                                 0M -         48 one        0
```

### OpenNebula Systems Marketplace[¶](broken-reference)

The OpenNebula Marketplace is a catalog of virtual appliances ready to run in OpenNebula environments available at [http://marketplace.opennebula.io/appliance](http://marketplace.opennebula.io/appliance).

#### Requirements[¶](broken-reference)

No additional requirements needed.

#### Configuration Attributes[¶](broken-reference)

| Attribute    | Description                                             |
| ------------ | ------------------------------------------------------- |
| `NAME`       | The name of the Marketplace. Default: OpenNebula Public |
| `MARKET_MAD` | `one`.                                                  |
| `ENDPOINT`   | The Marketplace endpoint URL                            |

### DockerHub Marketplace[¶](broken-reference)

The DockerHub Marketplace provide access to [DockerHub Official Images](https://hub.docker.com/search?image\_filter=official\&type=image). The OpenNebula context packages are installed during the import process so once an image is imported it’s fully prepared to be used.

Note

More information on how to use DockerHub images with the different hypervisors can be found here.

#### Requirements and limitations[¶](broken-reference)

* Docker must be installed and configured at the frontend. `oneadmin` user must have permissions for running docker.
* Approximately 6GB of storage plus the container image size.
* As images are builded in the OpenNebula Frontend node the architecture of this node will limit the images architecture.

Warning

OpenNebula service must be restarted after providing permissions to `oneadmin` for running docker.

#### Configuration Attributes[¶](broken-reference)

| Attribute    | Description                                     |
| ------------ | ----------------------------------------------- |
| `NAME`       | The name of the Marketplace. Default: DockerHub |
| `MARKET_MAD` | `dockerhub`                                     |

#### Downloading non official images[¶](broken-reference)

The DockerHub MarketPlace only list official images, if you need to use a non official image you can create an image (`oneimage create`) using as `PATH` (or `--path` option) an URL with the following format:

```
docker://<image>?size=<image_size>&filesystem=<fs_type>&format=raw&tag=<tag>&distro=<distro>
```

where the meaning of each option is described below:

| Argument       | Description                                                             |
| -------------- | ----------------------------------------------------------------------- |
| `<image>`      | DockerHub image name.                                                   |
| `<image_size>` | Resulting image size in MB. (It must be greater than actual image size) |
| `<fs_type>`    | Filesystem type (ext4, ext3, ext2 or xfs)                               |
| `<tag>`        | Image tag name (default `latest`).                                      |
| `<distro>`     | (Optional) image distribution.                                          |

Warning

OpenNebula finds out the image distribution automatically by running the container and checking `/etc/os-release` file. If this information is not available inside the container the `distro` argument have to be used.

For example, to create a new image called `nginx-dh` based on the `nginx` image from DockerHub with 3GB size using `ext4` and the `alpine` tag, you can use:

```
$ oneimage create --name nginx-dh --path 'docker://nginx?size=3072&filesystem=ext4&format=raw&tag=alpine' --datastore 1
  ID: 0
```

Note

This url format can also be used at Sunstone image creation dialog.

#### Entrypoint[¶](broken-reference)

When you download an application from the Dockerhub, OpenNebula will automatically inspect it to check if there is entrypoint information. This `ENTRYPOINT` and/or `CMD` commands are placed in the `/one_entrypoint.sh` script so it can be executed on boot. All the environment variables are passed into the script and can be further customized by the user by adding new values through context. Simply, add the environment variable as described by the documentation of the appliance in the `CONTEXT` section.

Note

You **have to** trigger the entrypoint execution in the `START_SCRIPT` with a line similar to: `nohup /one_entrypoint.sh &> /dev/null &`.

### Linux Containers MarketPlace[¶](broken-reference)

The [Linux Containers image server](https://images.linuxcontainers.org) hosts a public image server with container images for LXC and LXD. OpenNebula’s Linux Containers marketplace enable users to easily download, contextualize and add Linux containers images to an OpenNebula datastore.

Note

A log file (`/var/log/chroot.log`) is created inside the imported image filesystem with information about the operations done during the setup process; in case of issues it could be a useful source of information.

Note

More information on how to use Linux Containers images with the different hypervisors can be found here.

#### Requirements[¶](broken-reference)

* Approximately 6GB of storage plus the container image size.

#### Configuration Attributes[¶](broken-reference)

| Attribute       | Description                                 | Default                              |
| --------------- | ------------------------------------------- | ------------------------------------ |
| `NAME`          | Marketplace name (Required)                 |                                      |
| `MARKET_MAD`    | `linuxcontainers`                           |                                      |
| `ENDPOINT`      | The base URL of the Market.                 | `https://images.linuxcontainers.org` |
| `IMAGE_SIZE_MB` | Size in MB for the image holding the rootfs | `1024`                               |
| `FILESYSTEM`    | Filesystem used for the image               | `ext4`                               |
| `FORMAT`        | Image block file format                     | `raw`                                |
| `SKIP_UNTESTED` | Include only apps with support for context  | `yes`                                |
| `CPU`           | VMTemplate CPU                              | `1`                                  |
| `VCPU`          | VMTemplate VCPU                             | `2`                                  |
| `MEMORY`        | VMTemplate MEMORY                           | `768`                                |
| `PRIVILEGED`    | Secrurity mode of the Linux Container       | `true`                               |

### TurnKey Linux MarketPlace[¶](broken-reference)

[TurnKey Linux](https://www.turnkeylinux.org) is a free software repository that provides container images based on Debian. The TurnKey Linux Marketplace automatically installs OpenNebula context packages, so Images are ready to use.

Note

A log file (`/var/log/chroot.log`) is created inside the imported image filesystem with information about the operations done during the setup process; in case of issues it could be a useful source of information.

Note

More information on how to use Turnkey Linux images with the different hypervisors can be found here.

#### Requirements[¶](broken-reference)

* Approximately 6GB of storage plus the container image size configured on your frontend.

#### Configuration Attributes[¶](broken-reference)

| Attribute       | Description                                 | Default                   |
| --------------- | ------------------------------------------- | ------------------------- |
| `NAME`          | Marketplace name (Required)                 |                           |
| `MARKET_MAD`    | `turnkeylinux`                              |                           |
| `ENDPOINT`      | The base URL of the Market.                 | `http://turnkeylinux.org` |
| `IMAGE_SIZE_MB` | Size in MB for the image holding the rootfs | `1024`                    |
| `FILESYSTEM`    | Filesystem used for the image               | `ext4`                    |
| `FORMAT`        | Image block file format                     | `raw`                     |
| `SKIP_UNTESTED` | Include only apps with support for context  | `yes`                     |

### Disable Marketplace[¶](broken-reference)

Marketplace can be disabled with `onemarket disable`. By disabling a Marketplace all Appliances will be removed from OpenNebula, and it will be no longer monitored. Note that this process doesn’t affect already exported Images. After enabling the Marketplace with `onemarket enable`, it will be monitored again and all Aplliances from this Marketplace will show up again.
