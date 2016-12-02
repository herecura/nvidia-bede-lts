# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-bede-lts
pkgver=375.20
_extramodules=4.4-BEDE-LTS-external
_current_linux_version=4.4.36
_next_linux_version=4.5
pkgrel=4
pkgdesc="NVIDIA drivers for linux-bede-lts"
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
makedepends=(
    "linux-bede-lts>=$_current_linux_version"
    "linux-bede-lts-headers>=$_current_linux_version"
    "linux-bede-lts<$_next_linux_version"
    "linux-bede-lts-headers<$_next_linux_version"
    "nvidia-utils=$pkgver"
    "nvidia-libgl=$pkgver"
)
provides=('nvidia')
license=('custom')
options=(!strip)

source_i686=("http://download.nvidia.com/XFree86/Linux-x86/$pkgver/NVIDIA-Linux-x86-$pkgver.run")
source_x86_64=("http://download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run")
#source_i686=("NVIDIA-Linux-x86-$pkgver.run::https://developer.nvidia.com/linux32bit")
#source_x86_64=("NVIDIA-Linux-x86_64-$pkgver-no-compat32.run::https://developer.nvidia.com/linux64bit")
sha256sums_i686=('cc79d3ac2b688009ed2e47a1cf27557aea5dd745b3b6e9b83945c359ddab4335')
sha256sums_x86_64=('ef2e71b6eef6ce2ee2556c1449d129380f0312b5a6a51bd8483fd60f8df0b9bf')

[[ "$CARCH" = "i686" ]] && _pkg="NVIDIA-Linux-x86-${pkgver}"
[[ "$CARCH" = "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"
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
        "nvidia-libgl=$pkgver"
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

