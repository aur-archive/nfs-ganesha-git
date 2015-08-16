# Maintainer: Thomas Favre-Bulle <thomas.favre-bulle at bull dot net>
pkgname=nfs-ganesha-git
pkgver=2.2.rc3.r0.gf79e20f
pkgrel=1
pkgdesc="User space NFS server"
arch=(x86_64)
url="https://github.com/nfs-ganesha/nfs-ganesha/wiki"
license=('LGPL3')
depends=('dbus' 'git')
makedepends=('git' 'cmake' 'bison' 'flex' 'doxygen' 'krb5' 'dbus' 'xfsprogs' 'nfsidmap' 'libwbclient' 'libcap' 'python-pyqt4')
optdepends=('jemalloc: more scalable malloc implementation'
            'lttng-ust: LTTng tracing support (experimental)')
provides=('nfs-ganesha')
conflicts=('nfs-ganesha')
source=("$pkgname"::'git://github.com/nfs-ganesha/nfs-ganesha.git#branch=next'
        'dbus.nfs-ganesha.conf'
        'nfs-ganesha.service')
md5sums=('SKIP'
         '4cac68afb5067d17f573de348e0f58dd'
         '157ad5fa3c079a746851eabe8f27b776')

pkgver() {
  cd "$srcdir/$pkgname"
  # Use the tag of the last commit
  git describe --long | sed -r 's/^V//;s/([^-]*-g)/r\1/;s/-/./g'
}

prepare() {
  cd "$srcdir/$pkgname"
  git submodule init
  git config submodule.ntirpc.url $srcdir/src/libntirpc
  git submodule update
}

build() {
  cd "$srcdir/$pkgname/src"
  cmake -DCMAKE_BUILD_TYPE=Release \
        -DUSE_ADMIN_TOOLS=OFF \
        -DUSE_LTTNG=ON \
        -DUSE_FSAL_PROXY=ON \
        -DUSE_FSAL_VFS=ON \
        -DUSE_FSAL_XFS=ON \
        -DUSE_FSAL_GPFS=ON \
        -DUSE_DBUS=ON \
        -DUSE_9P=ON \
        -D_USE_9P=ON \
        -DCMAKE_INSTALL_PREFIX=/usr .
  make
}

package() {
  cd "$srcdir/$pkgname/src"
  make DESTDIR="$pkgdir/" install
  mkdir -p "$pkgdir/etc/ganesha"
  install -Dm644 "$srcdir/nfs-ganesha.service" "$pkgdir/usr/lib/systemd/system/nfs-ganesha.service"
  install -Dm644 "$srcdir/dbus.nfs-ganesha.conf" "$pkgdir/etc/dbus-1/system.d/nfs-ganesha.conf"
  install -Dm644 "$srcdir/$pkgname/src/LICENSE.txt" "$pkgdir/usr/share/licenses/nfs-ganesha/LICENSE.txt"
}
