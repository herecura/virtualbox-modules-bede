# Maintainer: Bartłomiej Piotrowski <nospam@bpiotrowski.pl>
# Contributor: Ionut Biru <ibiru@archlinux.org>
# Contributor: Sébastien Luttringer <seblu@aur.archlinux.org>

pkgbase=virtualbox-modules-bede
pkgname=('virtualbox-modules-bede-host' 'virtualbox-modules-bede-guest')
pkgver=6.0.4
_extramodules=4.20-BEDE-external
_current_linux_version=4.20.6
_next_linux_version=4.21
pkgrel=2
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
source=('modules-load-virtualbox-bede')
sha512sums=('e91bca3a219ea2fee594c43a9915d17381675dc3af4f0ba980b64e42fa7df28e38a7fcffa8089d8f859d532ae7b08ac7157afea4f3bf907136cb3abd1b4f4867')

# in case we need to do some patching
#build() {
    #_kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

    ## dkms need modification to be run as user
    #cp -Lr /var/lib/dkms .
    #echo "dkms_tree='$srcdir/dkms'" > dkms.conf

    #(
        #cd dkms/vboxhost/${pkgver}_OSE/source
        #patch -p1 -i "$srcdir/linux-4.20-vboxnetadp.patch"
        #patch -p1 -i "$srcdir/linux-4.20-time-r0drv-linux.patch"
        #patch -p1 -i "$srcdir/linux-4.20-iprt-time.patch"
    #)
    ## build host modules
    #dkms --dkmsframework dkms.conf build "vboxhost/${pkgver}_OSE" -k "$_kernver"
#}

package_virtualbox-modules-bede-host() {
    pkgdesc="Kernel host modules for VirtualBox (linux-bede)"
    license=('GPL')
    depends=(
        "linux-bede>=$_current_linux_version"
        "linux-bede<$_next_linux_version"
    )
    provides=('VIRTUALBOX-HOST-MODULES')

    _kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

    install -dm755 "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    # when dkms was used
    cd "/var/lib/dkms/vboxhost/${pkgver}_OSE/$_kernver/$CARCH/module"
    # when build is used
    #cd dkms/vboxhost/${pkgver}_OSE/$_kernver/$CARCH/module
    install -m644 * "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    find "${pkgdir}" -name '*.ko' -exec xz {} +

    # install config file in modules-load.d for out of the box experience
    install -Dm644 "$srcdir/modules-load-virtualbox-bede" \
        "$pkgdir/usr/lib/modules-load.d/virtualbox-modules-bede-host.conf"
}

package_virtualbox-modules-bede-guest() {
    pkgdesc="Kernel guest modules for VirtualBox (linux-bede)"
    license=('GPL')
	depends=(
        "linux-bede>=$_current_linux_version"
        "linux-bede<$_next_linux_version"
    )
    provides=('VIRTUALBOX-GUEST-MODULES')

    _kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

    install -dm755 "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    cd "/var/lib/dkms/vboxsf/${pkgver}_OSE/$_kernver/$CARCH/module"
    install -m644 * "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    find "${pkgdir}" -name '*.ko' -exec xz {} +
}

