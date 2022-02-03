# Parted Tutorial

## ESP Size:

###    The Boot Loader Specification which suggests that the ESP partition should be about 477 MiB (500 MB).
###    As per the Arch Linux wiki, to avoid potential problems with some EFIs, ESP size should be at least 512 MiB.
###    550 MiB is recommended to avoid MiB/MB confusion and accidentally creating FAT16.

#### parted: create partition
#### example 1 format
        parted /dev/sda print                                                # verify if label is present eg gpt or legacy bios
        parted -s -a optimal -- /dev/sda mklabel gpt                         # create gpt lable; -s; --script, -a; --align optimal, "--" no more options
        parted -s -a optimal -- /dev/sda mkpart ESP fat32 0% 550MiB          # ESP/EFI 0550MiB *start with 0% no other unit worked for me*
        parted -s -- /dev/sda set 1 esp on                                   # esp on = UEFI; boot on = BIOS
        parted -s -a optimal -- /dev/sda mkpart primary ext4 577MB 1577MB    # boot    1000MiB *inserted suggestion*
        parted -s -a optimal -- /dev/sda mkpart primary 1577MB 100%          # LVM    1000MiB *inserted suggestion*
#### example 2 format
        parted -s -a optimal -- /dev/sda \
        print \
        mklabel gpt \
        mkpart ESP fat32 0% 550MiB \
        set 1 esp on \
        mkpart primary ext4 577MB 1577MB \
        mkpart primary 1577MB 100%
#### example 3 format
        parted -s -a optimal -- /dev/sda print mklabel gpt mkpart ESP fat32 0% 550MiB set 1 esp on mkpart primary ext4 577MB 1577MB mkpart primary 1577MB 100% print         for i in {1..3}; do parted /dev/sda rm $i; done
