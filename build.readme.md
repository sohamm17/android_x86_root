# Building Project Celadon for the Quest Project

## Basic Download and BuildEnv Setup
0. Follow the download instructions at the [offical Celadon documentation](https://01.org/projectceladon/documentation/getting_started/build-source) for obtaining the source code.
1. Build depends on python2, and setting up `pyenv` with symlink shims does NOT solve the problem of having python3 as the default system python. For the build, the only way to get around it is to deleve the /bin/python/ symlink and point it to python2.
2. System might be missing some language and local packs. Run `export LC_ALL=C` as a workaround, but note that the resultant build will be missing many different unicode fonts that may cause text rendering issues for many languages.
3. Some systems, linke ArchLinux, use ncurses 6 as the default version of ncurses. For this, install `ncurses5-compat-libs` from AUR.
0. Build the flashfile (kernelflinger) option; GRUB installer image does not boot beyond a blank screen with a cursor due to unknown reasons.

## Build Issues and Modifications to Source
0. Running lunch command again for the celadon project exits during patch applicatoin with a path conflict error. This is not really an issue and can be safely ignored without any build errors.
1. Disabling TrustyOS: Following are the modifications that are needed to disable TrustyOS support for Celadon.
In file `device/intel/project-celadon/celadon/mixins.spec` : `trusty: false(ref_target=project-celadon_64)`
In file `device/intel/project-celadon/celadon/mixins.spec`: in `boot-arch` option, set `tos_partition=false`
2. For root terminal access, the code and configs require some pre-build changes; these are documented in the README that talks about getting root access.
https://lists.01.org/pipermail/celadon/2018-December/001439.html

## Flash and Install:
0. Unzip the celadon-flashfiles.zip onto a VFAT USB drive.
1. Edit the `installer.cmd` file and remove the `format data` as well as the `flashing lock` lines. Flashing lock option increases the install time by ~10 minutes. Since this is a change to the mixin spec, we must re-run the lunch command to trigger the mixing update. For this reason, it is recommended that this change be made first and foremost, since running lunch a second time results in patch application failure. Therefore, the ideal workflow for building Celadon for Quest project is:
download/setup env -> modify mixing spec and root permissions -> lunch -> make
