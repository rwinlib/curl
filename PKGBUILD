# Maintainer: Alexey Pavlov <alexpux@gmail.com>
#_variant=-openssl
_variant=-winssl
#_variant=-gnutls

if [ "${_variant}" = "-openssl" ]; then
  _namesuff=
else
  _namesuff="${_variant}"
fi

_realname=curl
pkgbase=mingw-w64-${_realname}
pkgname="${MINGW_PACKAGE_PREFIX}-${_realname}${_namesuff}"
pkgver=7.59.0
pkgrel=1
pkgdesc="Command line tool and library for transferring data with URLs. (mingw-w64)"
arch=('any')
url="https://curl.haxx.se/"
license=("MIT")
makedepends=("${MINGW_PACKAGE_PREFIX}-gcc" "${MINGW_PACKAGE_PREFIX}-pkg-config")
depends=("${MINGW_PACKAGE_PREFIX}-gcc-libs"
         "${MINGW_PACKAGE_PREFIX}-c-ares"
         "${MINGW_PACKAGE_PREFIX}-libidn2"
         "${MINGW_PACKAGE_PREFIX}-libmetalink"
         "${MINGW_PACKAGE_PREFIX}-libssh2"
         "${MINGW_PACKAGE_PREFIX}-zlib"
         $([[ "$_variant" == "-openssl" ]] && echo \
            "${MINGW_PACKAGE_PREFIX}-ca-certificates" \
            "${MINGW_PACKAGE_PREFIX}-openssl" \
            "${MINGW_PACKAGE_PREFIX}-nghttp2")
         $([[ "$_variant" == "-gnutls" ]] && echo \
            "${MINGW_PACKAGE_PREFIX}-rtmpdump" \
            "${MINGW_PACKAGE_PREFIX}-ca-certificates" \
            "${MINGW_PACKAGE_PREFIX}-gnutls")
         )
if [ -n "${_namesuff}" ]; then
  provides=("${MINGW_PACKAGE_PREFIX}-${_realname}")
  conflicts=("${MINGW_PACKAGE_PREFIX}-${_realname}")
fi
options=('staticlibs')
source=("${url}/download/${_realname}-${pkgver}.tar.bz2"{,.asc}
        "0001-Make-cURL-relocatable.patch")
sha256sums=('c92fe31a348eae079121b73884065e600c533493eb50f1f6cee9c48a3f454826'
            'SKIP'
            '93f636b569ba9089dd8280afefa48e4cf9c1fc5a5142bcc08ff181379231d2a4')
validpgpkeys=('914C533DF9B2ADA2204F586D78E11C6B279D5C91'  # Daniel Stenberg
              '27EDEAF22F3ABCEB50DB9A125CC908FDB71E12C2'
              '4461EAF0F8E9097F48AF0555F9FEAFF9D34A1BDB')

prepare() {
  cd "${_realname}-${pkgver}"
  rm -f lib/pathtools.h lib/pathtools.c > /dev/null 2>&1 || true
  autoreconf -vfi
}

build() {
  local -a extra_config
  if check_option "debug" "y"; then
    extra_config+=( --enable-debug )
  else
    extra_config+=( --disable-debug )
  fi
  mkdir -p "${srcdir}/build-${CARCH}"
  local -a _variant_config
  if [ "${_variant}" = "-winssl" ]; then
    _variant_config+=("--with-ssl")
    _variant_config+=("--without-gnutls")
    _variant_config+=("--with-winssl")
    _variant_config+=('--without-nghttp2')
    _variant_config+=("--without-ca-bundle")
    _variant_config+=("--without-ca-path")
    _variant_config+=("--without-librtmp")
  elif [ "${_variant}" = "-gnutls" ]; then
    _variant_config+=("--with-default-ssl-backend=gnutls")
    _variant_config+=("--without-ssl")
    _variant_config+=("--with-gnutls")
    _variant_config+=("--with-winssl")
    _variant_config+=('--without-nghttp2')
    _variant_config+=("--with-ca-bundle=${MINGW_PREFIX}/ssl/certs/ca-bundle.crt")
    _variant_config+=("--with-librtmp")
  elif [ "${_variant}" = "-openssl" ]; then
    _variant_config+=("--with-default-ssl-backend=openssl")
    _variant_config+=("--without-gnutls")
    _variant_config+=("--with-ssl")
    _variant_config+=("--with-winssl")
    _variant_config+=("--with-ca-bundle=${MINGW_PREFIX}/ssl/certs/ca-bundle.crt")
    _variant_config+=("--with-nghttp2=${MINGW_PREFIX}/")
    _variant_config+=("--without-librtmp")
  fi
  export curl_disallow_strtok_r="yes"
  cd "${srcdir}/build-${CARCH}"
  ../${_realname}-${pkgver}/configure \
    --prefix=${MINGW_PREFIX} \
    --build=${MINGW_CHOST} \
    --host=${MINGW_CHOST} \
    --target=${MINGW_CHOST} \
    --without-random \
    --with-libssh2 \
    --enable-static \
    --disable-shared \
    --enable-sspi \
    --enable-ldap \
    --enable-ldaps \
    --without-libidn2 \
    --with-winidn \
    --with-ldap-lib=wldap32 \
    --without-libmetalink \
    "${_variant_config[@]}" \
    "${extra_config[@]}"
# there's a bug with zsh completion generation script and Windows.
# curl has to specified with the file extension.
  sed -i "s|\/curl > \$\@|\/curl\$\{EXEEXT\} > \$\@|" scripts/Makefile
  make
}

package() {
  cd "${srcdir}/build-${CARCH}"
  make DESTDIR="${pkgdir}" install
  
  local PREFIX_DEPS=$(cygpath -am ${MINGW_PREFIX})
  sed -s "s|${PREFIX_DEPS}|${MINGW_PREFIX}|g" -i ${pkgdir}${MINGW_PREFIX}/bin/curl-config
}
