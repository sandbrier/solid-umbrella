# GPG CHEATSHEET

---
Compilied by **@sandbrier_** | GPG Fingerprint: 2082 4A33 3BEF FE49 2FCB  4739 57A7 464B FBE4 9BD1 | Credit to: [Madboa](https://www.madboa.com/geek/gpg-quickstart/), Riseup.net & [Scout3801](http://irtfweb.ifa.hawaii.edu/~lockhart/gpg/gpg-cs.html) | Last Updated: **20160313** | Download .txt file:

---
## Import / Export
#### Import Public Key via specified keyserver
```sh
gpg --keyserver keys.riseup.net --recv-key 0x4E0791268F7C67EABE88F1B03043E2B7139A768E
```

#### Import a Public Key
```sh
gpg --import public.key
```
This adds the public key in the file "public.key" to your public key ring.

#### Export a key to text file
```sh
gpg --armor --export alice@cyb.org
```

#### Export a private key:
```sh
gpg --export-secret-key -a "User Name" > private.key
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