# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Contributor: Massimiliano Torromeo <massimiliano.torromeo@gmail.com>

_linuxprefix=linux61

_module=r8168
pkgname="${_linuxprefix}-${_module}"
pkgver=8.056.02
pkgrel=10
pkgdesc="A kernel module for Realtek 8168 network cards"
arch=('x86_64')
url="https://www.realtek.com/Download/List?cate_id=584"
license=("GPL-2.0-only")
groups=("${_linuxprefix}-extramodules")
depends=('glibc' "${_linuxprefix}")
makedepends=("${_linuxprefix}-headers")
provides=("${_module}")
source=("https://github.com/mtorromeo/r8168/archive/$pkgver/${_module}-$pkgver.tar.gz"
        "https://github.com/mtorromeo/r8168/releases/download/$pkgver/${_module}-$pkgver.tar.gz.asc")
sha256sums=('f8354f4018ae11c651d7df737323c317f14ed6af68066372badeba57c99b7a12'
            'SKIP')
validpgpkeys=('0CADAACF70F64C654E131B3111675C743429DDEF') # Massimiliano Torromeo <massimiliano.torromeo@gmail.com>

prepare() {
  cd "${_module}-$pkgver"
}

build() {
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"

  cd "${_module}-$pkgver"

  # avoid using the Makefile directly -- it doesn't understand
  # any kernel but the current.

  make -C /usr/lib/modules/${_kernver}/build M="$srcdir/${_module}-$pkgver/src" \
    ENABLE_USE_FIRMWARE_FILE=y \
    CONFIG_R8168_NAPI=y \
    CONFIG_R8168_VLAN=y \
    CONFIG_ASPM=y \
    ENABLE_S5WOL=y \
    ENABLE_EEE=y \
    modules
}

package() {
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"

  cd "${_module}-$pkgver"
  install -Dm644 src/*.ko -t "$pkgdir/usr/lib/modules/${_kernver}/extramodules/"
  find "$pkgdir" -name '*.ko' -exec strip --strip-debug {} +
  find "$pkgdir" -name '*.ko' -exec zstd --rm -19 {} +

# We'll let mhwd-db handle blacklisting for now
#  echo "blacklist r8169" | \
#    install -Dm644 /dev/stdin "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
}
