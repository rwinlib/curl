This libcurl was built with msys2. We only added

	export curl_disallow_strtok_r="yes"
 	export CPPFLAGS+=" -DNGHTTP2_STATICLIB"

To the PKGBUILD before running ./configure to make the
library compatible with gcc-4.6.3.

All other sources were grabbed from msys2 repo except for
librtmp which was taken from the libcurl website.
