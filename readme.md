This libcurl was built with msys2. We only added

	export curl_disallow_strtok_r="yes"

The OpenSSL build has to be linked using:

    -lcurl -lssh2 -lz -lssl -lcrypto -lgdi32 -lws2_32 -lcrypt32 -lwldap32
	
The WinSSL (native SecureChannel) build has to be linked using:

    -lcurl -lz -lws2_32 -lcrypt32 -lwldap32
	
Both libs were compiled with native Windows SSPI and IDN.
