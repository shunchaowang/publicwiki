## keytool generate java keystore
    keytool -genkeypair -alias jwt -keyalg RSA -keypass wang22015 -keystore jwt.jks -storepass wang22015
    key and store have the same password of wang22015
## extract public key from keystore
    1. keytool -list -rfc --keystore jwt.jks | openssl x509 -inform pem -pubkey
        provide keystore pass as prompted
    2. copy all content between -----BEGIN PUBLIC KEY----- -----END PUBLIC KEY----- including these 2 lines into a new
       file to store public key
## load private key from file
```
import java.io.*;
import java.nio.*;
import java.security.*;
import java.security.spec.*;

public class PrivateKeyReader {

  public static PrivateKey get(String filename)
  throws Exception {

    byte[] keyBytes = Files.readAllBytes(Paths.get(filename));

    PKCS8EncodedKeySpec spec =
      new PKCS8EncodedKeySpec(keyBytes);
    KeyFactory kf = KeyFactory.getInstance("RSA");
    return kf.generatePrivate(spec);
  }
}
```
## load public key from file
```
import java.io.*;
import java.nio.*;
import java.security.*;
import java.security.spec.*;

public class PublicKeyReader {

  public static PublicKey get(String filename)
    throws Exception {

    byte[] keyBytes = Files.readAllBytes(Paths.get(filename));

    X509EncodedKeySpec spec =
      new X509EncodedKeySpec(keyBytes);
    KeyFactory kf = KeyFactory.getInstance("RSA");
    return kf.generatePublic(spec);
  }
}
```

