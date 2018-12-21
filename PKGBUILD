# Maintainer: Bartłomiej Piotrowski <nospam@bpiotrowski.pl>
# Contributor: Ionut Biru <ibiru@archlinux.org>
# Contributor: Sébastien Luttringer <seblu@aur.archlinux.org>

pkgbase=virtualbox-modules-bede
pkgname=('virtualbox-modules-bede-host')
pkgver=5.2.22
_extramodules=4.20-BEDE-external
_current_linux_version=4.20rc7
_next_linux_version=4.21
pkgrel=17
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
    'linux-4.20-vboxnetadp.patch'
    'linux-4.20-time-r0drv-linux.patch'
    'linux-4.20-iprt-time.patch'
)
sha512sums=('e91bca3a219ea2fee594c43a9915d17381675dc3af4f0ba980b64e42fa7df28e38a7fcffa8089d8f859d532ae7b08ac7157afea4f3bf907136cb3abd1b4f4867'
            '2e0a925a2bd13bf4e224ddbf1923effdfe673081e165927e9fc2a75550a2231f5262df26585d9efed79da3adff295cb631dd16831a4ece0ddea6d3b494809707'
            '1d6a02f985c0eb302c26f4a1ea3eba459c7cf4b5f7eaa393ab406001feea05c4f91bd32f7b1c5dc6a944cd0d1ca7241488445ba3e1585b2ed077b4941568ab8c'
            '24e6d5cf5a2550797bfff0706f42867ff18c9713c4842a2eb61310608f1392c81256bcdc428856234018590cdb9e48159373718da8f98ac3616646b39b874001'
            '412c6ddf51a02b2fe943daa08b70eabb55dc7118478f777851d468d6d607f25f5d0862f32dca53223772bb02c9f67cd4b06d475600841716d2a950bf9429d99e')

# in case we need to do some patching
build() {
    _kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

    # dkms need modification to be run as user
    cp -Lr /var/lib/dkms .
    echo "dkms_tree='$srcdir/dkms'" > dkms.conf

    (
        cd dkms/vboxhost/${pkgver}_OSE/source
        patch -p1 -i "$srcdir/linux-4.20-vboxnetadp.patch"
        patch -p1 -i "$srcdir/linux-4.20-time-r0drv-linux.patch"
        patch -p1 -i "$srcdir/linux-4.20-iprt-time.patch"
    )
    # build host modules
    dkms --dkmsframework dkms.conf build "vboxhost/${pkgver}_OSE" -k "$_kernver"
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
    find "${pkgdir}" -name '*.ko' -exec xz {} +

    # install config file in modules-load.d for out of the box experience
    install -Dm644 "$srcdir/modules-load-virtualbox-bede" \
        "$pkgdir/usr/lib/modules-load.d/virtualbox-modules-bede-host.conf"
}

