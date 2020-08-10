# Maintainer: Bartłomiej Piotrowski <nospam@bpiotrowski.pl>
# Contributor: Ionut Biru <ibiru@archlinux.org>
# Contributor: Sébastien Luttringer <seblu@aur.archlinux.org>

pkgbase=virtualbox-modules-bede
pkgname=('virtualbox-modules-bede-host')
pkgver=6.1.12
_current_linux_version=5.8
_next_linux_version=5.9
pkgrel=8
arch=('x86_64')
url='http://virtualbox.org'
license=('GPL')
makedepends=(
    "linux-bede>=$_current_linux_version"
    "linux-bede-headers>=$_current_linux_version"
    "linux-bede<$_next_linux_version"
    "linux-bede-headers<$_next_linux_version"
    "virtualbox-host-dkms>=$pkgver"
    "virtualbox-guest-dkms>=$pkgver"
)
source=('modules-load-virtualbox-bede'
        'kernel-5.8.patch'
        '94b92246ab29bd20add4ac96c4c88a9c65255b9e.patch')
sha512sums=('80bf2c8a402ab066fb70c39a241fa92494c07616fe347bd2ed35b68ddfddab1ceed6322493848f28400fc4706c631e7376646aac0bb572b686bf98aa6d8a8991'
            '6e2c4ad7744bf30295b69e704cab3c6389c0d6aa6715e035fd6202ad3db9b8ade3f88781b588d4aae5923218b566693a52ecc8ecf151cd691b80e44301f59b34'
            '990b82e849396d6e0ea6463477f211e36f42cd677ac477cd6f8b52a156dad8d701f38e8b1db7fe8ceb53d8a34341e6adab32408c74ed7a62956c6c816dff06bd')

# in case we need to do some patching
build() {
    _kernver="$(cat /usr/src/linux-bede/version)"

    # dkms need modification to be run as user
    cp -Lr /var/lib/dkms .
    echo "dkms_tree='$srcdir/dkms'" > dkms.conf

    (
        cd dkms/vboxhost/${pkgver}_OSE/source
        patch -p1 -i "$srcdir/kernel-5.8.patch"
    )
    cp -a /usr/bin/dkms ./dkms-bin
    patch -i "$srcdir/94b92246ab29bd20add4ac96c4c88a9c65255b9e.patch" dkms-bin
    # build host modules
    ./dkms-bin --dkmsframework dkms.conf build "vboxhost/${pkgver}_OSE" -k "$_kernver"
}

package_virtualbox-modules-bede-host() {
    pkgdesc="Kernel host modules for VirtualBox (linux-bede)"
    license=('GPL')
    depends=(
        "linux-bede>=$_current_linux_version"
        "linux-bede<$_next_linux_version"
    )
    provides=('VIRTUALBOX-HOST-MODULES')

    local kernver="$(</usr/src/linux-bede/version)"
    local extradir="/usr/lib/modules/$kernver/extramodules"

    # output dkms log for easier debugging
    #cat "/var/lib/dkms/vboxhost/${pkgver}_OSE/build/make.log"

    # when dkms was used
    #cd "/var/lib/dkms/vboxhost/${pkgver}_OSE/$kernver/$CARCH/module"
    # when build is used
    cd dkms/vboxhost/${pkgver}_OSE/$kernver/$CARCH/module
    install -dm755 "${pkgdir}${extradir}/$pkgname"
    install -Dm644 * "${pkgdir}${extradir}/$pkgname/"
    find "${pkgdir}" -name '*.ko' -exec xz {} +

    # install config file in modules-load.d for out of the box experience
    install -Dm644 "$srcdir/modules-load-virtualbox-bede" \
        "$pkgdir/usr/lib/modules-load.d/virtualbox-modules-bede-host.conf"
}

