MINGW_PACKAGE_PREFIX=mingw-w64
_pkgname=grpc
pkgname=${MINGW_PACKAGE_PREFIX}-$_pkgname
pkgver=1.16.0
_gtest_ver=1.8.1
pkgrel=1
pkgdesc='gRPC - Googles high performance, open source, general RPC framework (mingw-w64)'
arch=(any)
url='https://www.grpc.io/'
license=('BSD')
depends=("${MINGW_PACKAGE_PREFIX}-gcc"
         "${MINGW_PACKAGE_PREFIX}-c-ares"
         "${MINGW_PACKAGE_PREFIX}-gflags"
         "${MINGW_PACKAGE_PREFIX}-openssl"
         "${MINGW_PACKAGE_PREFIX}-protobuf"
         "${MINGW_PACKAGE_PREFIX}-zlib")
makedepends=("${MINGW_PACKAGE_PREFIX}-gcc"
             "${MINGW_PACKAGE_PREFIX}-cmake"
             "automake" "autoconf")
options=(!strip !buildflags staticlibs)
_sourcename=${_pkgname}-${pkgver}
source=("${_pkgname}-${pkgver}.tar.gz::https://github.com/google/grpc/archive/v${pkgver}.tar.gz"
        "googletest-release-${_gtest_ver}.tar.gz::https://github.com/google/googletest/archive/release-${_gtest_ver}.tar.gz")
sha256sums=('d99db0b39b490d2469a8ef74197d5f211fa740fc9581dccecbb76c56d080fce1'
            '9bf1fe5182a604b4135edc1a425ae356c9ad15e9b23f9f12a02e80184c3a249c')
_architectures="i686-w64-mingw32 x86_64-w64-mingw32"

build() {
  cd ${_sourcename}

  sed -i 's!\$<TARGET_FILE:grpc_cpp_plugin>!/usr/bin/grpc_cpp_plugin!' CMakeLists.txt
  sed -i 's/target_link_libraries(address_sorting/target_link_libraries(address_sorting\n  ws2_32/' CMakeLists.txt
  for _arch in ${_architectures}; do
    mkdir -p build-${_arch}
    pushd build-${_arch}
    ${_arch}-cmake .. \
      -DCMAKE_BUILD_TYPE=Release \
      -DREGISTER_INSTALL_PREFIX=OFF \
      -DBUILD_SHARED_LIBS=ON \
      -DBUILD_STATIC_LIBS=ON \
      -DBUILD_TESTING=ON \
      -DBUILD_SHARED_LIBS="ON" \
      -DgRPC_ZLIB_PROVIDER="package" \
      -DgRPC_CARES_PROVIDER="package" \
      -DgRPC_PROTOBUF_PROVIDER="package" \
      -DgRPC_SSL_PROVIDER="package" \
      -DgRPC_GFLAGS_PROVIDER="package" \
      -DgRPC_BENCHMARK_PROVIDER="package" \
      -DCMAKE_CXX_FLAGS=" -D_WIN32_WINNT=0x0600" \
      -DCMAKE_C_FLAGS=" -D_WIN32_WINNT=0x0600" \
      -DCMAKE_CPP_FLAGS=" -D_WIN32_WINNT=0x0600"
    make
    popd
  done
}

package() {
  cd ${_sourcename}
  for _arch in ${_architectures}; do
    pushd build-${_arch}
    make DESTDIR=${pkgdir} install
    popd
  done
}
