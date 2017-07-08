This libcurl was built with msys2. The OpenSSL build has to be linked using:

    -lcurl -lssh2 -lz -lssl -lcrypto -lgdi32 -lws2_32 -lcrypt32 -lwldap32
	
The WinSSL (native SecureChannel) build has to be linked using:

    -lcurl -lz -lws2_32 -lcrypt32 -lwldap32	
	
Both builds are configured with:

	--with-winidn
	--enable-sspi
	--without-librtmp 
	--without-libidn2

We also used the following vars:

	export curl_disallow_strtok_r="yes"
 	export CPPFLAGS="-DUSE_WIN32_IDN -DWINVER=0x0600"	

The latter is needed to work around an autoconf bug which does not correclty detect winidn on mingw32.
