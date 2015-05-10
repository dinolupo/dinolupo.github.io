---
author: dinolupo
comments: true
date: 2007-06-14 10:56:22+00:00
layout: post
slug: exporting-private-key-from-jdk-keystore-jks
title: Exporting private key from JDK keystore (JKS)
wordpress_id: 5
categories:
- Java - Security
---

  Compile and run the following to export the private key from JDK or JRE keystore. After exported, if you want to use that  in Apache SSL module, you must convert it with  the following command:  
  
openssl pkcs8 -inform PEM -nocrypt -in exported-pkcs8.key -out exported.key  
  
here is the java code:  
<!--more-->
 {% highlight java linenos %} 
import java.io.File;
import java.io.FileInputStream;
import java.io.FileWriter;
import java.security.Key;
import java.security.KeyPair;
import java.security.KeyStore;
import java.security.KeyStoreException;
import java.security.NoSuchAlgorithmException;
import java.security.PrivateKey;
import java.security.PublicKey;
import java.security.UnrecoverableKeyException;
import java.security.cert.Certificate;

import sun.misc.BASE64Encoder;

public class ExportPrivateKey {
        private File keystoreFile;
        private String keyStoreType;
        private char[] password;
        private String alias;
        private File exportedFile;

        public static KeyPair getPrivateKey(KeyStore keystore, String alias, char[] password) {
                try {
                        Key key=keystore.getKey(alias,password);
                        if(key instanceof PrivateKey) {
                                Certificate cert=keystore.getCertificate(alias);
                                PublicKey publicKey=cert.getPublicKey();
                                return new KeyPair(publicKey,(PrivateKey)key);
                        }
                } catch (UnrecoverableKeyException e) {
        } catch (NoSuchAlgorithmException e) {
        } catch (KeyStoreException e) {
        }
        return null;
        }

        public void export() throws Exception{
                KeyStore keystore=KeyStore.getInstance(keyStoreType);
                BASE64Encoder encoder=new BASE64Encoder();
                keystore.load(new FileInputStream(keystoreFile),password);
                KeyPair keyPair=getPrivateKey(keystore,alias,password);
                PrivateKey privateKey=keyPair.getPrivate();
                String encoded=encoder.encode(privateKey.getEncoded());
                FileWriter fw=new FileWriter(exportedFile);
                fw.write("-----BEGIN PRIVATE KEY-----n");
                fw.write(encoded);
                fw.write("n");
                fw.write("-----END PRIVATE KEY-----");
                fw.close();
        }

        public static void main(String args[]) throws Exception {
            if (args.length<5) {
              System.out.println("nnUse:njava ExportPrivateKey {keystore_path} JKS {keystore_password} {alias} {target_file}nn");
              return;
            }
                ExportPrivateKey export=new ExportPrivateKey();
                export.keystoreFile=new File(args[0]);
                export.keyStoreType=args[1];
                export.password=args[2].toCharArray();
                export.alias=args[3];
                export.exportedFile=new File(args[4]);
                export.export();
        }
}           
{% endhighlight %}