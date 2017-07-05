# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede-lts
pkgver=381.22
_extramodules=4.9-BEDE-LTS-external
_current_linux_version=4.9.36
_next_linux_version=4.10
pkgrel=42
pkgdesc="NVIDIA drivers for linux-bede-lts"
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
makedepends=(
    "linux-bede-lts>=$_current_linux_version"
    "linux-bede-lts-headers>=$_current_linux_version"
    "linux-bede-lts<$_next_linux_version"
    "linux-bede-lts-headers<$_next_linux_version"
    "nvidia-utils=$pkgver"
)
provides=('nvidia')
license=('custom')
options=(!strip)

source_i686=("http://download.nvidia.com/XFree86/Linux-x86/$pkgver/NVIDIA-Linux-x86-$pkgver.run")
source_x86_64=("http://download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run")
sha512sums_i686=('15723bfa25b0f39224ad5098c784a292abe4bf1daafaeb2f1df910bbab466e666b848b12e50b0bdd45f83e2d81957425a63501550b3dc5eb8fe3e576a8a10d22')
sha512sums_x86_64=('c40214725d8b02dab2596fb5b8d22033fbd15b29ccb8d0c789a049e3251b301c5001b778b29958f8d424625b42d4dedf3f70a47493ea8c67c63060c0b54dc7b5')

[[ "$CARCH" == "i686" ]] && _pkg="NVIDIA-Linux-x86-${pkgver}"
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
    _kernver="$(cat /usr/lib/modules/$_extramodules/version)"
    cd $_folder/kernel
    make SYSSRC=/usr/lib/modules/$_kernver/build module
}

package() {
    depends=(
        "linux-bede-lts>=$_current_linux_version"
        "linux-bede-lts<$_next_linux_version"
        "nvidia-utils=$pkgver"
    )

    install -Dm644 "$srcdir/$_folder/kernel/nvidia.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-modeset.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia-modeset.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-drm.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia-drm.ko"

    if [[ "$CARCH" = "x86_64" ]]; then
        install -D -m644 "${srcdir}/${_folder}/kernel/nvidia-uvm.ko" \
            "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia-uvm.ko"
    fi

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

    # gzip all modules
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;
}

