# Maintainer: mikezackles <mikezackles@gmail.com>
# Contributor: Martchus <martchus@gmx.net>
# Contributor: emersion <contact@emersion.fr>

# This package contains a cross toolchain to compile for MacOSX. It bundles:
# * MacOSX 10.15 SDK
# * wrapper for Clang provided by osxcross

# Adjust this to point to your Xcode download. This PKGBUILD assumes it is a .xip that 
_xcode=Xcode_11.3.xip
_sdkname=MacOSX10.15.sdk

pkgname=osxcross
_revccount=348
_commit=542acc2
pkgver=$_revccount.$_commit
pkgrel=1
pkgdesc='OS X cross toolchain for Linux, FreeBSD and NetBSD'
arch=('x86_64')
url='https://github.com/tpoechtrager/osxcross'
license=('MIT')
depends=()
makedepends=('git' 'libxml2' 'clang>=3.2' 'cmake' 'python' 'libc++')
optdepends=('clang>=3.2: Use Clang (rather than GCC)'
            'llvm: Link Time Optimization support and ld64 -bitcode_bundle support'
            'uuid: ld64 -random_uuid support'
            'xar: ld64 -bitcode_bundle support')
provides=("$pkgname")
conflicts=("$pkgname")
source=(
  "git+https://github.com/tpoechtrager/$pkgname.git#commit=$_commit"
  'meson.cross'
)
sha256sums=(
  'SKIP'
  '91443cf30c3b4c64412e149280009a1003c9cb2ae1a7c3e8c2483b668b8eb532'
)
noextract=("$_xcode")
options=('!strip')

build() {
  cd "$srcdir/$pkgname"

  msg2 'Extract SDK from Xcode'
  ./tools/gen_sdk_package_pbzx.sh "../../$_xcode"
  mv "$_sdkname.tar.xz" tarballs/

  msg2 'Build osxcross'
  UNATTENDED=yes OSX_VERSION_MIN=10.11 ./build.sh
}

package() {
  cd "$srcdir/$pkgname"

  mkdir -p "$pkgdir/opt"
  cp -r target "$pkgdir/opt/osxcross"
  local cppdir="$pkgdir/opt/osxcross/SDK/$_sdkname/usr/include/c++"
  # Replace any libc++ headers shipped with Xcode with those for the installed libc++
  rm -rf "$cppdir/v1"
  mkdir -p "$cppdir"
  ln -s /usr/include/c++/v1 "$cppdir/v1"

  # Install meson cross file
  install -Dm644 "$srcdir/meson.cross" "$pkgdir/usr/local/share/meson/cross/macos"
}
