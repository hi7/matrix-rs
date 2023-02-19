# matrix-rs
UEFI app written in Rust

# build
`cargo build --target x86_64-unknown-uefi`

# Debian/Ubuntu QEMU & OVMF
`sudo apt-get install qemu ovmf`
`cp /usr/share/OVMF/OVMF_CODE.fd .`
`cp /usr/share/OVMF/OVMF_VARS.fd .`

# Fedora QEMU & OVMF
`sudo dnf install qemu-kvm edk2-ovmf`
`cp /usr/share/edk2/ovmf/OVMF_CODE.fd .`
`cp /usr/share/edk2/ovmf/OVMF_VARS.fd .`

# copy efi app to ESP
`cp target/x86_64-unknown-uefi/debug/matrix-rs.efi esp/efi/boot/bootx64.efi`

# run in QEMU
```
qemu-system-x86_64 -enable-kvm \
    -drive if=pflash,format=raw,readonly=on,file=OVMF_CODE.fd \
    -drive if=pflash,format=raw,readonly=on,file=OVMF_VARS.fd \
    -drive format=raw,file=fat:rw:esp
```

# On Hardware
Create the GPT, create a 9MB partition starting at 1MB, and set the
partition type to EFI System.
```
sgdisk \
    --clear \
    --new=1:1M:10M \
    --typecode=1:C12A7328-F81F-11D2-BA4B-00A0C93EC93B \
    /path/to/disk
```

# Format the partition as FAT.
`mkfs.fat /path/to/disk_partition`

# Mount the partition.
`mkdir esp`
`mount /path/to/disk_partition esp`

# Create the boot directory.
`mkdir esp/EFI/BOOT`

# Copy in the boot executable.
`cp /path/to/your-executable.efi esp/EFI/BOOT/BOOTX64.EFI`
