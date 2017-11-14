# vim:set ft=sh et:
# $Id$
# Maintainer: Bartłomiej Piotrowski <nospam@bpiotrowski.pl>
# Contributor: Ionut Biru <ibiru@archlinux.org>
# Contributor: Sébastien Luttringer <seblu@aur.archlinux.org>

pkgbase=virtualbox-modules-bede
pkgname=('virtualbox-modules-bede-host' 'virtualbox-modules-bede-guest')
pkgver=5.2.0
_extramodules=4.14-BEDE-external
_current_linux_version=4.14
_next_linux_version=4.15
pkgrel=6
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
    '60-vboxguest.rules'
    'virtualbox-modules-5.1.28-udp.patch'
    'linux-4.14-guest.patch')
sha512sums=('e91bca3a219ea2fee594c43a9915d17381675dc3af4f0ba980b64e42fa7df28e38a7fcffa8089d8f859d532ae7b08ac7157afea4f3bf907136cb3abd1b4f4867'
            '2e0a925a2bd13bf4e224ddbf1923effdfe673081e165927e9fc2a75550a2231f5262df26585d9efed79da3adff295cb631dd16831a4ece0ddea6d3b494809707'
            '79e6444b64db1da2aea727233de91ce6ff6075eb8bea1098ca8430445a50b50c036aa9e27317c568bdb6b7f22bb8fa177e97caaea1701c06a9709ee8a3133a51'
            '1874672b2b012e1ee2f5a932a0ff35a29a7fd362b2e3d549a6349b1bc09663c6f620ed8eb42555641480becec001df74c5d80bf4374849afc7518c2c71ab8ac3')

# in case we need to do some patching
build() {
    _kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

    # dkms need modification to be run as user
    cp -Lr /var/lib/dkms .
    echo "dkms_tree='$srcdir/dkms'" > dkms.conf

    (
        cd dkms/vboxhost/${pkgver}_OSE/source
        patch -p1 -i "$srcdir/virtualbox-modules-5.1.28-udp.patch"
    )
    # build host modules
    dkms --dkmsframework dkms.conf build "vboxhost/${pkgver}_OSE" -k "$_kernver"
    (
        cd dkms
        patch -p1 -i "$srcdir/linux-4.14-guest.patch"
    )
    # build guest modules
    dkms --dkmsframework dkms.conf build "vboxguest/${pkgver}_OSE" -k "$_kernver"
}

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
    #cd "/var/lib/dkms/vboxhost/${pkgver}_OSE/$_kernver/$CARCH/module"
    # when build is used
    cd dkms/vboxhost/${pkgver}_OSE/$_kernver/$CARCH/module
    install -m644 * "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} +

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
    # when dkms was used
    #cd "/var/lib/dkms/vboxguest/${pkgver}_OSE/$_kernver/$CARCH/module"
    # when build is used
    cd dkms/vboxguest/${pkgver}_OSE/$_kernver/$CARCH/module
    install -m644 * "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} +

    install -D -m 0644 "$srcdir/60-vboxguest.rules" \
        "$pkgdir/usr/lib/udev/rules.d/60-vboxguest-bede.rules"
}

