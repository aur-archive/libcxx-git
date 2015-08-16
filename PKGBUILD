# Maintainer: Jianjun Mao <justmao945_at_gmail.com>

pkgname=libcxx-git
pkgver=20130123
pkgrel=1
pkgdesc="A new implementation of the C++ standard library targeting C++0X."
arch=('i686' 'x86_64')
url="http://libcxx.llvm.org/"
license=('MIT')
depends=('gcc-libs')
makedepends=('clang' 'cmake' 'git' 'gcc')
conflicts=('libcxx' 'libcxx-svn')
install='libcxx.install'
_gitroot="http://llvm.org/git/libcxx"
_gitname="libcxx"

build() {
  cd "$srcdir"
  msg "Connecting to GIT server...."
  if [[ -d "$_gitname" ]]; then
    cd "$_gitname" && git pull origin
    msg "The local files are updated."
  else
    git clone --depth=1 "$_gitroot" "$_gitname"
  fi
  msg "GIT checkout done or server timeout"
  msg "Starting build..."

  cd ${srcdir}/${_gitname}
  
  # build/include and build/inlcude/bits are required to generate libsupc++ abi headers.
  rm -rf build && mkdir -p build/include/bits && cd build

  # delete unsupported flags by clang
  CXXFLAGS=`echo ${CXXFLAGS} | sed 's/\(-D_FORTIFY_SOURCE=2\)\|\(--param=ssp-buffer-size=4\)//g'`

  # get GCC(G++) version and target
  GCC_VER=`g++ -v 2>&1 | grep 'gcc version' | cut -d ' ' -f 3`
  GCC_TAR=`g++ -v 2>&1 | grep 'Target' | cut -d ' ' -f 2`

  # set libcxx abi to libsupc++ and define its headers
  cmake .. -DCMAKE_INSTALL_PREFIX="$pkgdir/usr" -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ -DLIBCXX_CXX_ABI="libsupc++" -DLIBCXX_LIBSUPCXX_INCLUDE_PATHS="/usr/include/c++/${GCC_VER};/usr/include/c++/${GCC_VER}/${GCC_TAR}" && make
}

package() {
  cd "$srcdir/${_gitname}/build"
  make install

  cd "$srcdir/$_gitname"
  install -Dm644 LICENSE.TXT "$pkgdir/usr/share/licenses/${pkgname}/LICENSE"
}

