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