# Archived

This project is no longer maintained. Please use [upstream project](https://github.com/JasonN3/build-container-installer) moving forward.

# Universal Blue ISO Generator
This action is used to generate the ISO images for any Fedora Atomic based OCI images (bootc based images not currently supported). This project relies on the command feature in Anaconda `ostreecontainer`.

## Makefile
A Makefile is provided for ease of use. There are separate targets for each file generated, however `make` can be used to generate the final image and `make clean` can be used to clean up the workspace.

See [Customizing](#customizing) for information about customizing the image that gets created.

## Container
A container with the necessary tools already installed is provided at `ghcr.io/ublue-os/isogenerator:latest`

To use the container file, run `docker run --privileged --volume .:/isogenerator/output ghcr.io/ublue-os/isogenerator`.

This will create an ISO with the baked in defaults of the container image.

See [Customizing](#customizing) for information about customizing the image that gets created. The variable can either be defined as environment variables or as command arguments.
Examples:

Creating Universal Blue Silverblue ISO
```bash
docker run --rm --privileged --volume .:/isogenerator/output -e VERSION=39 -e IMAGE_NAME=silverblue-main -e IMAGE_TAG=latest -e VARIANT=Silverblue ghcr.io/ublue-os/isogenerator:39
```
```bash
# Requires sudo to run
sudo podman run --rm --privileged --volume .:/isogenerator/output -e VERSION=39 -e IMAGE_NAME=silverblue-main -e IMAGE_TAG=latest -e VARIANT=Silverblue ghcr.io/ublue-os/isogenerator:39
```

Creating Universal Blue Kinoite ISO
```bash
docker run --rm --privileged --volume .:/isogenerator/output -e VERSION=39 -e IMAGE_NAME=kinoite-main -e IMAGE_TAG=latest -e VARIANT=Kinoite ghcr.io/ublue-os/isogenerator:39
```
```bash
# Requires sudo to run
sudo podman run --rm --privileged --volume .:/isogenerator/output -e VERSION=39 -e IMAGE_NAME=kinoite-main -e IMAGE_TAG=latest -e VARIANT=Kinoite ghcr.io/ublue-os/isogenerator:39
```

## Customizing

### Inputs

The following variables can be used to customize the create image.

| Variable            | Description                                                  | Default Value          |
| -----------------   | ------------------------------------------------------------ | ---------------------- |
| ARCH                | Architecture for image to build                              | x86_64                 |
| VERSION             | Fedora version of installer to build                         | 39                     |
| IMAGE_REPO          | Repository containing the source container image             | ghcr.io/ublue-os       |
| IMAGE_NAME          | Name of the source container image                           | base-main              |
| IMAGE_TAG           | Tag of the source container image                            | *VERSION*              |
| EXTRA_BOOT_PARAMS   | Extra params used by grub to boot the anaconda installer     | \[empty\]              |
| VARIANT             | Source container variant\*                                   | Kinoite                |
| WEB_UI              | Enable Anaconda WebUI (experimental)                         | false                  |
| ENROLLMENT_PASSWORD | Password used to enroll secure boot key into BIOS\*\*        | isogenerator           |
| SECURE_BOOT_KEY_URL | URL used to download your secure boot key for enrollment\*\* | \[empty\]              |

\*Available options for VARIANT can be found by running `dnf provides system-release`. Variant will be the third item in the package name. Example: `fedora-release-kinoite-39-34.noarch` will be kinoite

\*\*NOTE: ENROLLMENT_PASSWORD and SECURE_BOOT_KEY_URL are not required. They are only required if you are creating specific kernel modules or if you are using Universal Blue Kernel Modules.

Our public key for our kmods is located here: https://github.com/ublue-os/akmods/raw/main/certs/public_key.der

### Outputs

This action outputs some useful values for you to use further on in your workflow.

| Output | Description |
| ------ | ----------- |
| output-directory | The directory containing ISO and checksum files |
| iso-path | The full path to the ISO file |
| checksum-path | The full path to the checksum file |

## VSCode Dev Container
There is a dev container configuration provided for development. By default it will use the existing container image available at `ghcr.io/ublue-os/isogenerator`, however, you can have it build a new image by editing `.devcontainer/devcontainer.json` and replacing `image` with `build`. `Ctrl+/` can be used to comment and uncomment blocks of code within VSCode.

The code from VSCode will be available at `/workspaces/isogenerator` once the container has started.

Privileged is required for access to loop devices for lorax.

Use existing image
```json
{
	"name": "Existing Dockerfile",
	// "build": {
	// 	"context": "..",
	// 	"dockerfile": "../Containerfile",
	// 	"args": {
	// 		"version": "39"
	// 	}
	// },
	"image": "ghcr.io/ublue-os/isogenerator:latest",
	"overrideCommand": true,
	"shutdownAction": "stopContainer",
	"privileged": true
}
```

Build a new image
```json
{
	"name": "Existing Dockerfile",
	"build": {
		"context": "..",
		"dockerfile": "../Containerfile",
		"args": {
			"version": "39"
		}
	},
	//"image": "ghcr.io/ublue-os/isogenerator:latest",
	"overrideCommand": true,
	"shutdownAction": "stopContainer",
	"privileged": true
}
```

# Special Thanks
This code was originally forked from https://github.com/JasonN3/build-container-installer and adapted for the needs of Universal Blue.
