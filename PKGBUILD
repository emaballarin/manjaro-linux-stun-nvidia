# Based on the file created for Arch Linux by:
# Maintainer : Thomas Baechler <thomas@archlinux.org>

# Maintainer: Philip Müller <philm@manjaro.org>

_linuxprefix=linux419
_extramodules=extramodules-4.19-MANJARO
pkgname=$_linuxprefix-nvidia
_pkgname=nvidia
pkgver=396.51
pkgrel=0
epoch=1
provides=("$_pkgname=$pkgver")
groups=("$_linuxprefix-extramodules")
pkgdesc="NVIDIA drivers for linux."
arch=('x86_64')
url="http://www.nvidia.com/"
depends=("$_linuxprefix" "nvidia-utils=${epoch}:${pkgver}")
makedepends=("$_linuxprefix-headers")
conflicts=('nvidia-96xx' 'nvidia-173xx')
license=('custom')
install=nvidia.install
options=(!strip)
durl="http://us.download.nvidia.com/XFree86/Linux-x86"
source_x86_64=("${durl}_64/${pkgver}/NVIDIA-Linux-x86_64-${pkgver}-no-compat32.run")
sha256sums_x86_64=('9889d3dadaba49631ce1348ca9cc02349d12a50a718e9b629b0a0478fb681c6d')

[[ "$CARCH" = "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    sh "${_pkg}.run" --extract-only
    cd "${_pkg}"
    # patches here
    # drm: Drop DRM_CONTROL_ALLOW from ioctls
    sed -i -e 's/DRM_CONTROL_ALLOW|//g' kernel/nvidia-drm/nvidia-drm-drv.c
    # GPL-incompatible module nvidia.ko uses GPL-only symbol '__put_devmap_managed_page
    sed -i -e 's/MODULE_LICENSE("NVIDIA")/MODULE_LICENSE("GPL")/g' kernel/nvidia/{nv,nv-frontend}.c
    sed -i -e 's/MODULE_LICENSE("MIT")/MODULE_LICENSE("GPL")/g' kernel/nvidia-uvm/uvm_{common,unsupported}.c
    sed -i -e 's/MODULE_LICENSE("MIT")/MODULE_LICENSE("GPL")/g' kernel/nvidia-drm/nvidia-drm-linux.c
}

build() {
    _kernver="$(cat /usr/lib/modules/${_extramodules}/version)"
    cd "${_pkg}"/kernel
    make SYSSRC=/usr/lib/modules/"${_kernver}/build" module
}

package() {
    install -D -m644 "${srcdir}/${_pkg}/kernel/nvidia.ko" \
        "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia.ko"
    install -D -m644 "${srcdir}/${_pkg}/kernel/nvidia-modeset.ko" \
         "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia-modeset.ko"
    install -D -m644 "${srcdir}/${_pkg}/kernel/nvidia-drm.ko" \
         "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia-drm.ko"
    if [[ "$CARCH" = "x86_64" ]]; then
        install -D -m644 "${srcdir}/${_pkg}/kernel/nvidia-uvm.ko" \
            "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia-uvm.ko"
    fi
    gzip "${pkgdir}/usr/lib/modules/${_extramodules}/"*.ko
    sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='${_extramodules}'/" "${startdir}/nvidia.install"
}
