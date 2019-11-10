# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede-lts
pkgver=435.21
_extramodules=4.19-BEDE-LTS-external
_current_linux_version=4.19.83
_next_linux_version=4.20
pkgrel=15
pkgdesc="NVIDIA drivers for linux-bede-lts"
arch=('x86_64')
url="http://www.nvidia.com/"
makedepends=(
    "linux-bede-lts>=$_current_linux_version"
    "linux-bede-lts-headers>=$_current_linux_version"
    "linux-bede-lts<$_next_linux_version"
    "linux-bede-lts-headers<$_next_linux_version"
    "nvidia-utils=$pkgver"
    "libglvnd"
)
provides=('nvidia')
license=('custom')
options=(!strip)

source=("http://download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run")
sha512sums=('ecfdfab81007ca80d6034047d7871002ea24ad628ca973d2d7196270782608eb3af09c3c695e58bbc1c7995705349cf30438ca390aea238eaa2d108c694db2f2')

[[ "$CARCH" == "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"
#_folder=${_pkg//-no-compat32/}
_folder=${_pkg}

prepare() {
    [ -d "$_folder" ] && rm -rf "$_folder"
    sh $_pkg.run --extract-only
    cd $_folder
    # patch if needed
}

build() {
    cd $_folder/kernel
    make SYSSRC=/usr/src/linux-bede-lts
}

package() {
    depends=(
        "linux-bede-lts>=$_current_linux_version"
        "linux-bede-lts<$_next_linux_version"
        "nvidia-utils=$pkgver"
    )

    local extradir="/usr/lib/modules/$(</usr/src/linux-bede-lts/version)/extramodules"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia.ko" \
        "${pkgdir}${extradir}/$_pkgname/nvidia.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-modeset.ko" \
        "${pkgdir}${extradir}/$_pkgname/nvidia-modeset.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-drm.ko" \
        "${pkgdir}${extradir}/$_pkgname/nvidia-drm.ko"

    if [[ "$CARCH" = "x86_64" ]]; then
        install -D -m644 "${srcdir}/${_folder}/kernel/nvidia-uvm.ko" \
            "${pkgdir}${extradir}/$_pkgname/nvidia-uvm.ko"
    fi

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

    find "${pkgdir}" -name '*.ko' -exec xz {} +
}

