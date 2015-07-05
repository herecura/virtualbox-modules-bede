# $Id$
# Maintainer: Bartłomiej Piotrowski <nospam@bpiotrowski.pl>
# Contributor: Ionut Biru <ibiru@archlinux.org>
# Contributor: Sébastien Luttringer <seblu@aur.archlinux.org>

pkgbase=virtualbox-modules-bede
pkgname=('virtualbox-modules-bede-host' 'virtualbox-modules-bede-guest')
pkgver=4.2.16
pkgrel=11
arch=('i686' 'x86_64')
url='http://virtualbox.org'
license=('GPL')
makedepends=('linux-bede>=3.11' 'linux-bede<3.12' 'linux-bede-headers>=3.11' 'linux-bede-headers<3.12'
    "virtualbox-host-dkms>=$pkgver"
    "virtualbox-guest-dkms>=$pkgver")
source=('modules-load-virtualbox-bede'
    '60-vboxguest.rules'
    'kernel_3.11_guest.patch'
    'kernel_3.11_host.patch')
md5sums=('f2200ed91b6ec089d16cc3ada5418c73'
    'ed1341881437455d9735875ddf455fbe'
    'ee35534e60fe34d1a518d73be2ba01b8'
    '7622e5e32b34c1f1283b64c6ec98503f')

_extramodules=3.11-BEDE-external

build() {
    _kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

    # dkms need modification to be run as user
    cp -Lr /var/lib/dkms .
    echo "dkms_tree='$srcdir/dkms'" > dkms.conf
    # build host modules
    msg2 'Host modules'
    (
        cd "dkms/vboxhost/$pkgver/source/"
        patch -Np1 -i "$srcdir/kernel_3.11_host.patch"
    )
    dkms --dkmsframework dkms.conf build "vboxhost/$pkgver" -k "$_kernver"
    # build guest modules
    msg2 'Guest modules'
    (
        cd "dkms/vboxguest/$pkgver/source/"
        patch -Np1 -i "$srcdir/kernel_3.11_guest.patch"
    )
    dkms --dkmsframework dkms.conf build "vboxguest/$pkgver" -k "$_kernver"
}

package_virtualbox-modules-bede-host() {
    pkgdesc="Kernel host modules for VirtualBox (linux-bede)"
    license=('GPL')
    install=virtualbox-modules-bede-host.install
    depends=('linux-bede>=3.11' 'linux-bede<3.12')
    provides=("virtualbox-host-modules=$pkgver")

    _kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

    install -dm755 "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    cd "dkms/vboxhost/$pkgver/$_kernver/$CARCH/module"
    install -m644 * "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} +

    # install config file in modules-load.d for out of the box experience
    install -Dm644 "$srcdir/modules-load-virtualbox-bede" \
        "$pkgdir/usr/lib/modules-load.d/virtualbox-modules-bede-host.conf"

    sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='${_extramodules}'/" "$startdir/virtualbox-modules-bede-host.install"
}

package_virtualbox-modules-bede-guest() {
    pkgdesc="Kernel guest modules for VirtualBox (linux-bede)"
    license=('GPL')
    install=virtualbox-modules-bede-guest.install
    depends=('linux-bede>=3.11' 'linux-bede<3.12')
    provides=("virtualbox-guest-modules=${pkgver}")

    _kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

    install -dm755 "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    cd "dkms/vboxguest/$pkgver/$_kernver/$CARCH/module"
    install -m644 * "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} +

    install -D -m 0644 "$srcdir/60-vboxguest.rules" \
        "$pkgdir/usr/lib/udev/rules.d/60-vboxguest-bede.rules"

    sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='${_extramodules}'/" "$startdir/virtualbox-modules-bede-guest.install"
}

# vim:set ft=sh et:
