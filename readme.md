# 1
Firebase and Firestore using straight Cocoapods on High Sierra.

Steps after a git pull:
* bundle
* rake pod:install
* rake

Crashes during startup on 11.3 with:
```
Undefined symbols for architecture x86_64:
  "_ASN1_STRING_to_UTF8", referenced from:
      peer_from_x509(x509_st*, int, tsi_peer*) in libgRPC-Core.a(ssl_transport_security.o)
  "_BIO_free_all", referenced from:
      aes_gcm_format_errors(char const*, char**) in libgRPC-Core.a(aes_gcm.o)
  "_BIO_get_mem_data", referenced from:
      peer_from_x509(x509_st*, int, tsi_peer*) in libgRPC-Core.a(ssl_transport_security.o)
  "_BIO_get_mem_ptr", referenced from:
      aes_gcm_format_errors(char const*, char**) in libgRPC-Core.a(aes_gcm.o)
  "_BIO_new_bio_pair", referenced from:
      create_tsi_ssl_handshaker(ssl_ctx_st*, int, char const*, tsi_ssl_handshaker_factory*, tsi_handshaker**) in libgRPC-Core.a(ssl_transport_security.o)
```

etc etc


# 2
Now, if we vendor_project BoringSSL by uncommenting this line in the Rakefile:
```
app.vendor_project('resources/BoringSSL.framework', :static, :products => ['BoringSSL'], :headers_dir => 'Headers')  
```

It runs perfectly on 11.3, but crashes on 10.3.1 with:
```
dyld: Library not loaded: /usr/lib/libboringssl.dylib
  Referenced from: /Users/Brian/Library/Developer/CoreSimulator/Devices/E1FA7737-A73E-4311-86C2-F90F7AE66AAC/data/Containers/Bundle/Application/5045E02B-DE67-4399-BB25-FF986E3286A2/firestore-issue.app/firestore-issue
  Reason: no suitable image found.  Did find:
  /usr/lib/libboringssl.dylib: mach-o, but not built for iOS simulator
```
