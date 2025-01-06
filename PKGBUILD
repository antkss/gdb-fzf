# Contributor: Lukas Jirkovsky <l.jirkovsky@gmail.com>
# Contributor: Allan McRae <allan@archlinux.org>
# Maintainer: Stefan Husmann <stefan-husmann@t-online.de>

pkgname=('gdb-fzf' 'gdb-common-fzf')
pkgver=14.0.50.r114330.4bbb4dfbed1
pkgrel=1
epoch=1
pkgdesc="The GNU Debugger from git"
arch=('i686' 'x86_64')
url="http://www.gnu.org/software/gdb/"
license=('GPL3')
makedepends=('git')
provides=('gdb-common' 'gdb')
conflicts=('gdb-common' 'gdb')
options=('!libtool')
# source=('gdb::git+https://sourceware.org/git/binutils-gdb.git')
# md5sums=('SKIP')
prepare() {
	cd $srcdir
	wget "https://sourceware.org/pub/gdb/releases/gdb-15.2.tar.xz"
	tar -xf gdb-15.2.tar.xz
	mv gdb-15.2 gdb
}
pkgver() {
  cd "$srcdir/gdb"
  echo "15.2"

  # _ver="$(cat gdb/version.in | cut -dD -f1)"
  # _rev="$(git rev-list --count HEAD)"
  # _hash="$(git rev-parse --short HEAD)"
  # printf '%s' "${_ver}r${_rev}.${_hash}"
}

build() {
  cd "$srcdir/gdb"
  wget "https://raw.githubusercontent.com/filipkilibarda/gdb_fzf_patch/refs/heads/master/gdb.patch"
  git config --global user.email "lmao@gmail.com"
  git config --global user.name "lmaodark"
  git config --global init.defaultBranch main
	git init
	git add .
	git commit -m "initial commit" > /dev/null
	git apply ./gdb.patch 
  [[ -d build ]] || mkdir -p build
  cd build
  CFLAGS+=" -fcommon -fno-strict-overflow -Wno-stack-usage" CXXFLAGS+=" -fpermissive" ../configure \
    --prefix=/usr \
    --disable-nls \
    --disable-gprofng \
    --enable-tui \
    --with-system-readline \
    --with-guile=guile-3.0 \
    --with-system-gdbinit=/etc/gdb/gdbinit \
	--disable-werror
  CFLAGS=-Wno-error make -j4
}

package_gdb-fzf() {
  depends=('expat' 'guile' 'ncurses' 'xz' 'python' 'source-highlight'
	   'mpfr' 'xxhash' 'libelf' 'gdb-common-git')
  provides=('gdb')
  conflicts=('gdb')
  backup=(etc/gdb/gdbinit)

  cd "$srcdir/gdb/build"
  
  # install only gdb, otherwise it would install files already provided by binutils
  make DESTDIR="$pkgdir/" install-gdb

  # install "custom" system gdbinit
  install -dm755 "$pkgdir"/etc/gdb
  touch "$pkgdir"/etc/gdb/gdbinit
  # comes from gdb-common
  rm -r "$pkgdir/usr/share/gdb/"
}

package_gdb-common-fzf() {
  provides=('gdb-common')
  conflicts=('gdb-common')
  depends=('python-pygments' 'guile')
  cd "$srcdir/gdb/build"
  make -C gdb/data-directory DESTDIR="$pkgdir" install
}
