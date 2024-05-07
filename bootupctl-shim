#!/bin/bash

if [[ $# -ge 2 ]]; then
    if [[ "$1" == "backend" && "$2" == "install" ]]; then
        # BASH_ARGV[0] is the last argument. In the case of bootupctl backend install, it's the path to the target
        # root directory.
        echo "Copying Raspberry Pi firmware files to ${BASH_ARGV[0]}/boot/efi/" >&2
        cp -av /usr/lib/bootc-raspi-firmwares/. "${BASH_ARGV[0]}"/boot/efi/
        echo "Copying Raspberry Pi firmware files finished" >&2
    fi
fi

exec /usr/bin/bootupctl-orig/bootupctl "$@"
