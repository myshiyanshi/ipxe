# iPXE

## Introduce

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

### custom

    /src/config/console.h
     CONSOLE_VMWARE - VMware logfile console
     #define CONSOLE_VMWARE

    LOG_LEVEL - Log message level
    #define LOG_LEVEL LOG_ALL

    CONSOLE_SYSLOG - Syslog console
    #define CONSOLE_SYSLOG   CONSOLE_USAGE_LOG

    make parameter : DEBUG=scsi:3,iscsi,image

### host

Quick start guide:

   cd src
   make

For any more detailed instructions, see http://ipxe.org

### docker

    docker run -it --rm --name ipxe-build -v ${PWD}:/ipxe willglynn/ipxe-build-image sh

container shell:

    cd /ipxe/src && apk add syslinux && make
    make bin/undionly.kpxe
    make bin/undionly.kpxe EMBED=/ipxe/scripts/script.ipxe
    make bin/ipxe.iso
    make bin-x86_64-efi/ipxe.efi DEBUG=scsi:3,iscsi,image
    cp src/bin-x86_64-efi/ipxe.efi ../tftproot/ipxe-202106.efi

## deploy

router dhcp config file: dnsmasq.conf

    dhcp-match=set:iPXE,175
    dhcp-boot=tag:!iPXE,ipxe.efi,,192.168.1.122     #tftp
    dhcp-boot=tag:iPXE,http://pxeserver/menu.ipxe   #http

## ipxe client

    boot ipxe.efi
    chain http://${pxeserver}/boot.ipxe

## other

docker pull xbgmsharp/ipxe-buildweb
docker run  -d \
    --publish 8080:80 \
    --publish 9999:22 \
    --name ipxe-buildweb \
    xbgmsharp/ipxe-buildweb
