# iPXE

## 简介

iPXE is the leading open source network boot firmware. It provides a full PXE implementation enhanced with additional features such as:

- boot from a web server via HTTP  
- boot from an iSCSI SAN  
- boot from a Fibre Channel SAN via FCoE  
- boot from an AoE SAN  
- boot from a wireless network
- boot from a wide-area network
- boot from an Infiniband network
- control the boot process with a script

You can use iPXE to replace the existing PXE ROM on your network card, or you can chainload into iPXE to obtain the features of iPXE without the hassle of reflashing.

iPXE is free, open-source software licensed under the GNU GPL (with some portions under GPL-compatible licences), and is included in products from several network card manufacturers and OEMs.

## ipxe rom build

    docker run -it --name ipxe-build \
        -v ${PWD}/ipxe:/ipxe \
        willglynn/ipxe-build-image sh

    container shell:

    cd /ipxe/src && make
    make bin/undionly.kpxe
    make bin/undionly.kpxe EMBED=/ipxe/scripts/script.ipxe
    make bin/ipxe.iso
    make bin-x86_64-efi/ipxe.efi DEBUG=scsi:3,iscsi

## deploy

    dnsmasq.conf

    dhcp-match=set:iPXE,175
    dhcp-boot=tag:!iPXE,ipxe.efi,,192.168.1.122
    dhcp-boot=tag:iPXE,http://pxeserver/menu.ipxe

## ipxe client

    boot to ipxe.efi
    chain http://${pxeserver}/boot.ipxe




