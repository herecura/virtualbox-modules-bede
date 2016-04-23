# vim:set ft=sh et:
# $Id$
# Maintainer: Bartłomiej Piotrowski <nospam@bpiotrowski.pl>
# Contributor: Ionut Biru <ibiru@archlinux.org>
# Contributor: Sébastien Luttringer <seblu@aur.archlinux.org>

pkgbase=virtualbox-modules-bede
pkgname=('virtualbox-modules-bede-host' 'virtualbox-modules-bede-guest')
pkgver=5.0.16
pkgrel=8
arch=('i686' 'x86_64')
url='http://virtualbox.org'
license=('GPL')
makedepends=('linux-bede>=4.5.2' 'linux-bede<4.6' 'linux-bede-headers>=4.5' 'linux-bede-headers<4.6'
    "virtualbox-host-dkms>=$pkgver"
    "virtualbox-guest-dkms>=$pkgver")
source=('modules-load-virtualbox-bede'
    '60-vboxguest.rules')
sha256sums=('9b3c4dc5385fb3b4aeb841043384879c5c7ee926f5343d6a4177e913604f869d'
            '033c597e0f5285d2ddb0490868e5b6f945f45c7b1b1152a02a9e6fea438b2c95')

_extramodules=4.5-BEDE-external

package_virtualbox-modules-bede-host() {
    pkgdesc="Kernel host modules for VirtualBox (linux-bede)"
    license=('GPL')
    install=virtualbox-modules-bede-host.install
    depends=('linux-bede>=4.5' 'linux-bede<4.6')
    provides=('VIRTUALBOX-HOST-MODULES')

    _kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

    install -dm755 "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    cd "/var/lib/dkms/vboxhost/${pkgver}_OSE/$_kernver/$CARCH/module"
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
    depends=('linux-bede>=4.5' 'linux-bede<4.6')
    provides=('VIRTUALBOX-GUEST-MODULES')

    _kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

    install -dm755 "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    cd "/var/lib/dkms/vboxguest/${pkgver}_OSE/$_kernver/$CARCH/module"
    install -m644 * "$pkgdir/usr/lib/modules/$_extramodules/vbox"
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} +

    install -D -m 0644 "$srcdir/60-vboxguest.rules" \
        "$pkgdir/usr/lib/udev/rules.d/60-vboxguest-bede.rules"

    sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='${_extramodules}'/" "$startdir/virtualbox-modules-bede-guest.install"
}

