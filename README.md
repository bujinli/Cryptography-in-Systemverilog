# Cryptography in SystemVerilog

A collection of Cryptographic algorithms implemented in SystemVerilog.

## Reference

- [NIST FIPS 197 - Advanced Encryption Standard (AES)](http://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.197.pdf)
- [DATA ENCRYPTION STANDARD (DES)](http://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf)
- [Recommendation for the Triple Data Encryption Algorithm (TDEA) Block Cipher](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-67r1.pdf)
- [Recommendation for Block Cipher Modes of Operation A: Methods and Techniques](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-38a.pdf)
- [Recommendation for Block Cipher Modes of Operation B: The CMAC Mode for Authentication](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-38B.pdf)
- [Recommendation for Block Cipher Modes of Operation C: The CCM Mode for Authentication and Confidentiality](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-38c.pdf)
- [Recommendation for Block Cipher Modes of Operation D: Galois/Counter Mode (GCM) and GMAC](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-38d.pdf)

- [The MD5 Message-Digest Algorithm](https://www.rfc-editor.org/rfc/rfc1321.txt)
- [Secure Hash Standard (SHS)](http://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf)
- [The Keyed-Hash Message Authentication Code (HMAC)](http://csrc.nist.gov/publications/fips/fips198/fips-198a.pdf)






## API & Usage

```
// aes_pkg/aes_pkg.sv
    typedef CoreAES#(128) AES128;
    typedef CoreAES#(192) AES192;
    typedef CoreAES#(256) AES256;
```

Use `AES128`, `AES192`, and `AES256` to instantiate object handles with each carries AES basic encrytion and decryption functions using 128/192/256-bit key.

A demo in `top.sv`:

```
    bit[7:0] dout[], din[], key[];
    aes_pkg::AES128 aes_128;
    ...
    aes_128 = new();
    ...
    din = {>>byte{128'h00112233445566778899aabbccddeeff}};
    key = {>>byte{128'h000102030405060708090a0b0c0d0e0f}};
    aes_128.setKey(key);
    aes_128.encrypt(din, dout);
    din = {>>byte{128'h69c4e0d86a7b0430d8cdb78070b4c55a}};
    aes_128.decrypt(din, dout);
```

```
    function new();
```

Method `new()` instantiates an AES object.


```
    function void setKey (const ref bit[7:0] kin[]);
```

Method `setKey` sets the key with fixed key size. With wrong key size, this method would abort simulation with a fatal error. Key is passed with array of bytes, that's to say, an AES128/192/256 object needs 16/24/32 bytes key.

```
    function void encrypt (const ref bit[7:0] din[], ref bit[7:0] dout[]);
    function void decrypt (const ref bit[7:0] din[], ref bit[7:0] dout[]);
```

Method `encrypt` encrypts a block size, 128 bits and 16 bytes,  passed with `din`, and return cipher text with the same size to `dout`. `encrypt` uses latched key, if no `setKey` called before `encrypt`, it would abort simulation with a fatal error. The same thing would happen if a plain text with size other than 16 bytes is passed. User is in charge of 128 bit block segmentation and padding, if message need to be encrypt is with arbitrary length.

Method `decrypt` do the decryption on the same manner.

**IMPORTANT**: Add `<your_dir>/aes_pkg/aes_pkg.sv` to your compiling filelist, and lines `aes_pkg.sv` ahead of files referencing AES classes. And add option `+incdir+<your_dir>/aes_pkg` to your compile command. 

## Logging Issue

`aes_obj.setLogging()` turns on logging when `aes_obj` performs key expansion, encrytion or decryption. 
`aes_obj.setMuted() shuts the logging, and that is the default behavior of an `aes_obj`.
Note that `setLogging()` and `setMuted()` are static methods.
You can add `+define+NO_LOG` to compiling command to remove all logging facilities.

