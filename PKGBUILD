# Maintainer: você :)
pkgname=risemode-display
pkgver=1.0.0
pkgrel=1
pkgdesc="Driver Linux para o display de temperatura dos coolers Rise Mode (VID 1a2c PID 4984)"
arch=('any')
license=('MIT')
depends=('python' 'tk')
source=("risemode-display" "risemode-gui" "risemode-display.service" "99-risemode-display.rules")
sha256sums=('SKIP' 'SKIP' 'SKIP' 'SKIP')

package() {
  install -Dm755 "$srcdir/risemode-display" "$pkgdir/usr/bin/risemode-display"
  install -Dm755 "$srcdir/risemode-gui" "$pkgdir/usr/bin/risemode-gui"
  install -Dm644 "$srcdir/risemode-display.service" "$pkgdir/usr/lib/systemd/system/risemode-display.service"
  install -Dm644 "$srcdir/99-risemode-display.rules" "$pkgdir/usr/lib/udev/rules.d/99-risemode-display.rules"
}
