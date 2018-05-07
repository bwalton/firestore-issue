# Firebase Firestore on High Sierra.

## 1. Using straight Cocoapods

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


## 2. vendor_project trick
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

## 3. Static frameworks
These aren't included in this project, but we can also use the static frameworks from Firebase themselves:
https://firebase.google.com/docs/ios/setup#frameworks

Throw them in /vendor, then update your Rakefile:
```
# General libs
app.vendor_project('vendor/Firebase/Analytics/nanopb.framework', :static, :products => ['nanopb'], :headers_dir => 'Headers')  
app.vendor_project('vendor/Firebase/Firestore/BoringSSL.framework', :static, :products => ['BoringSSL'], :headers_dir => 'Headers')  
app.vendor_project('vendor/Firebase/Firestore/gRPC-Core.framework', :static, :products => ['gRPC-Core'], :headers_dir => 'Headers')  
app.vendor_project('vendor/Firebase/Firestore/gRPC-RxLibrary.framework', :static, :products => ['gRPC-RxLibrary'], :headers_dir => 'Headers')  
app.vendor_project('vendor/Firebase/Firestore/gRPC.framework', :static, :products => ['gRPC'], :headers_dir => 'Headers')  
app.vendor_project('vendor/Firebase/Firestore/gRPC-ProtoRPC.framework', :static, :products => ['gRPC-ProtoRPC'], :headers_dir => 'Headers')  

# Analytics
app.vendor_project('vendor/Firebase/Analytics/FirebaseCore.framework', :static, :products => ['FirebaseCore'], :headers_dir => 'Headers')  
app.vendor_project('vendor/Firebase/Analytics/FirebaseNanoPB.framework', :static, :products => ['FirebaseNanoPB'])  
app.vendor_project('vendor/Firebase/Analytics/FirebaseCoreDiagnostics.framework', :static, :products => ['FirebaseCoreDiagnostics'])  
app.vendor_project('vendor/Firebase/Analytics/FirebaseInstanceID.framework', :static, :products => ['FirebaseInstanceID'], :headers_dir => 'Headers')  
app.vendor_project('vendor/Firebase/Analytics/GoogleToolboxForMac.framework', :static, :products => ['GoogleToolboxForMac'], :headers_dir => 'Headers')  
app.vendor_project('vendor/Firebase/Analytics/FirebaseAnalytics.framework', :static, :products => ['FirebaseAnalytics'], :headers_dir => 'Headers')  

# Auth
app.vendor_project('vendor/Firebase/Auth/FirebaseAuth.framework', :static, :products => ['FirebaseAuth'], :headers_dir => 'Headers')  
app.vendor_project('vendor/Firebase/Auth/GTMSessionFetcher.framework', :static, :products => ['GTMSessionFetcher'], :headers_dir => 'Headers')  

# Database
app.vendor_project('vendor/Firebase/Database/FirebaseDatabase.framework', :static, :products => ['FirebaseDatabase'], :headers_dir => 'Headers')  
app.vendor_project('vendor/Firebase/Database/leveldb-library.framework', :static, :products => ['leveldb-library'], :headers_dir => 'Headers')  

# Firestore
app.vendor_project('vendor/Firebase/Firestore/leveldb-library.framework', :static, :products => ['leveldb-library'], :headers_dir => 'Headers')   
app.vendor_project('vendor/Firebase/Firestore/Protobuf.framework', :static, :products => ['Protobuf'], :headers_dir => 'Headers')  
app.vendor_project('vendor/Firebase/Firestore/FirebaseFirestore.framework', :static, :products => ['FirebaseFirestore'], :headers_dir => 'Headers')  
```

These are in a super specific order that I figured out through magic and alchemy. Now! This will compile but we get an error about the Invite section of Firebase (which you can see that we don't actually use). This same error was mentioned here:
https://github.com/firebase/firebase-ios-sdk/issues/1110

Their solution was to pull the pods direct from source, but that put me in the same boat as before. Using the static frameworks seem to get me closer so I commented in the above thread asking about that. They said that the statics will get updated in the next release (5.0). So maybe that'll work?
