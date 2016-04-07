# THE GPG CHEATSHEET

---
Last Updated: **April 7, 2016**

---
### Suggestions for getting the most out of Command Line
1. **Key IDs** - The majority of these commands require you to specifiy the key to act upon. That is done via a KEY ID. The Key ID is *usually* expressed by name, long ID, or short ID.They are interchangeable. For example all 3 of the following would work in a command.
    * sandbrier@openmailbox.org
    * 0xF962DCC50EFD680E
    * 0x0EFD680E
2. **Filenames** - Key management can become challenging if you don't have a good standard for filenames. This is the format that works for me: email_pubkey_KEYID.xyz and email_PRIVKEY_KEYID.xyz. For example my pubkey file is named sandbrier_pubkey_MASTER_0xF962DCC50EFD680E.key.
    * Consider using CAPS to distinguish PRIVKEYS from pubkeys
    * Consider putting PRIVKEYs and Revocation keys in a subfolder

## Import / Export
#### Import Public Key via specified keyserver
Note that the :80 at the end of the server address specifies HTTP as the outbound port and will often resolve issues.
```sh
gpg --keyserver keys.riseup.net --recv-key 0x4E0791268F7C67EABE88F1B03043E2B7139A768E
gpg --keyserver sks-keyservers.net:80 --recv-key 0x4E0791268F7C67EABE88F1B03043E2B7139A768E
gpg ---keyserver x-hkp://pool.sks-keyservers.net --recv-keys 0x4E2C6E8793298290

```

#### Import a Public Key
```sh
gpg --import public.key
```
This adds the public key in the file "public.key" to your public key ring.

#### Export/Backup Public Key
```sh
gpg --armor --export alice@cyb.org
gpg --armor --export 0xF962DCC50EFD680E > filename.key
gpg --armor --export 0xF962DCC50EFD680E > filename.gpg
gpg --armor --export 0xF962DCC50EFD680E > filename.txt
```

#### Export/Backup PRIVATE key:
```sh
gpg --armor --export-secret-key 0xF962DCC50EFD680E  > filename.key
gpg --armor --export-secret-key 0xF962DCC50EFD680E  > filename.gpg
gpg --armor --export-secret-key 0xF962DCC50EFD680E  > filename.txt
```

#### Export/Backup PRIVATE SUBkey(s):
```sh
gpg --export-secret-subkeys 0xF962DCC50EFD680E > filename.key
```

#### Export Trust values:
```sh
gpg --export-ownertrust > filename.txt
```

## Fingerprints
#### See Key Fingerprint
```sh
gpg --fingerprint KEYID
```
#### To generate a short list of numbers that you can use via an alternative method to verify a public key, use:
```sh
gpg --fingerprint > fingerprint
```
This creates the file fingerprint with your fingerprint info.

#### See who has trusted (signed) a key
```sh
gpg --list-sigs 0x4E0791268F7C67EABE88F1B03043E2B7139A768E
```

#### Verify a signed file
Notes: Put both the file and the signature file (.asc) in the same folder. You'll want to import the public key prior to running the verification.
```sh
gpg --verify file.exe.asc file.exe
gpg --verify file.exe.sig file.exe
```
Output should say "Good signature from..." & "Signature made DATE by KEYID", don't worry about the warning about the trusted signature. That is related to the trust level of the Key not the validity of the signature.

#### Create a signed file (detached)
This is best for signing a binary/executable file as it creates a new (detached) .sig signature file
```sh
gpg --detach-sign filename
```

#### Change the passphrase of the secret key
```sh
gpg --edit-key YOUR_KEYID_HERE
gpg> passwd
gpg> save
```

#### Trust a key
```sh
gpg --edit KEYID
gpg> trust
gpg> (choose lvl 1-5)
gpg> quit
```

#### Delete a public key (from your public key ring):
```sh
gpg --delete-key KEYID
```
This removes the public key from your public key ring.
NOTE! If there is a private key on your private key ring associated with this public key, you will get an error! You must delete your private key for this key pair from your private key ring first.

#### Delete an private key (a key on your private key ring):
```sh
gpg --delete-secret-key KEYID
```
This deletes the secret key from your secret key ring.

## List Keys
#### List the keys in your public key ring:
```sh
gpg --list-keys
```

#### List the keys in your *secret* key ring:
```sh
gpg --list-secret-keys
```
## REVOCATION
4 steps to revoking your GPG key and publishing the revocation.
1. **Create a Revocation Key**
```sh
 gpg --gen-revoke KEYID
```
2. **Copy Revocation Key**
You’re asked if you want to provide a reason for the revocation (key comprised, superseded or no longer used) and an optional free-text description. After supplying your passphrase, an ascii-armoured key block is printed out. Paste this text into a file
```sh
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1.2.4 (GNU/Linux)
Comment: A revocation certificate should follow

iGwEIBECACwFAkAKbmwlHQJLZXkgd2FzIG9uIGEgbGFwdG9wIHRoYXQgd2FzIHN0
b2xlbgAKCRBQw2pwY4IoXlv4AJ0XgWhSuSwv2jpd2ifFA5IXyijnEACfXfn/qtfq
KyMdShD0odXAliKD43w=
=mRL+
-----END PGP PUBLIC KEY BLOCK-----
```
3. **Locally import Revocation key**
```sh
gpg --import my_revocation.txt
```
4. **Upload Revocation Key**
```sh
gpg --keyserver pgp.mit.edu --send-keys 6382285E
```

## ENCRYPT / DECRYPT
#### To encrypt data, use:
Simple version
```sh
gpg --encrypt --recipient 'myfriend@his.isp.net' foo.txt
```
Using Options
```sh
gpg --encrypt --u "Sender User Name" --recipient "Receiver User Name" somefile
```
There are some useful options here, such as -u to specify the secret key to be used, and -r to specify the public key of the recipient.
As an example: gpg -e -u "Charles Lockhart" -r "A Friend" mydata.tar
This should create a file called "mydata.tar.gpg" that contains the encrypted data. I think you specify the senders username so that the recipient can verify that the contents are from that person (using the fingerprint?).
NOTE!: mydata.tar is not removed, you end up with two files, so if you want to have only the encrypted file in existance, you probably have to delete mydata.tar yourself.
An interesting side note, I encrypted the preemptive kernel patch, a file of 55,247 bytes, and ended up with an encrypted file of 15,276 bytes.

#### To decrypt data, use:
```sh
gpg -d mydata.tar.gpg
(OR)
gpg --output foo.txt --decrypt foo.txt.gpg
```

If you have multiple secret keys, it'll choose the correct one, or output an error if the correct one doesn't exist. You'll be prompted to enter your passphrase. Afterwards there will exist the file "mydata.tar", and the encrypted "original," mydata.tar.gpg.

---
Compilied by **[@sandbrier_](https://twitter.com/sandbrier_)** 

GPG Fingerprint: 3AC9 97AF 897E E5F2 ABF7  2644 F962 DCC5 0EFD 680E

Credits: [Madboa](https://www.madboa.com/geek/gpg-quickstart/), [GnuPG Mini Howto](http://www.dewinter.com/gnupg_howto/english/GPGMiniHowto.html), Riseup.net, [chrisroos](https://gist.github.com/chrisroos/1205934), [Alex Cabal](https://alexcabal.com/creating-the-perfect-gpg-keypair/), [Matt Biddulph](https://www.hackdiary.com/2004/01/18/revoking-a-gpg-key/) & [Scout3801](http://irtfweb.ifa.hawaii.edu/~lockhart/gpg/gpg-cs.html)
