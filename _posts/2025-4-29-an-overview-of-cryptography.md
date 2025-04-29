---
title: An Overview of Cryptography
date: 2025-4-29
tags: cryptography
categories: [CS, crypto]
---
### INTRODUCTION

In the early-1990s, when the commercial Internet was still young (!), security was taken seriously by most users. Many thought that increased security provided comfort to paranoid people while most computer professionals realized that security provided some very basic protections that we all needed? Cryptography for the masses barely existed at that time and was certainly not a topic of common discourse. By the turn of the century, of course, the Internet had grown in size and importance so as to be the provider of essential communication between billions of people around the world and is the ubiquitous tool for commerce, social interaction, and the exchange of an increasing amount of personal information — and we even have a whole form of currency named for cryptography!

Security and privacy impacts many applications, ranging from secure commerce and payments to private communications and protecting health care information. One essential aspect for secure communications is that of cryptography. But it is important to note that while cryptography is _necessary_ for secure communications, it is not by itself _sufficient_. The reader is advised, then, that the topics covered here only describe the first of many steps necessary for better security in any number of situations.

This paper has two major purposes. The first is to define some of the terms and concepts behind basic cryptographic methods, and to offer a way to compare the myriad cryptographic schemes in use today. The second is to provide some real examples of cryptography in use today. (See [Section A.4](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#manual) for some additional commentary on this...)

**DISCLAIMER:** Several companies, products, and services are mentioned in this tutorial. Such mention is for example purposes only and, unless explicitly stated otherwise, should not be taken as a recommendation or endorsement by the author.

### BASIC CONCEPTS OF CRYPTOGRAPHY

_Cryptography_ — the science of secret writing — is an ancient art; the first documented use of cryptography in writing dates back to circa 1900 B.C. when an Egyptian scribe used non-standard hieroglyphs in an inscription. Some experts argue that cryptography appeared spontaneously sometime after writing was invented, with applications ranging from diplomatic missives to war-time battle plans. It is no surprise, then, that new forms of cryptography came soon after the widespread development of computer communications. In data and telecommunications, cryptography is necessary when communicating over any untrusted medium, which includes just about _any_ network, particularly the Internet.

There are five primary functions of cryptography:

1.  _Privacy/confidentiality:_ Ensuring that no one can read the message except the intended receiver.
2.  _Authentication:_ The process of proving one's identity.
3.  _Integrity:_ Assuring the receiver that the received message has not been altered in any way from the original.
4.  _Non-repudiation:_ A mechanism to prove that the sender really sent this message.
5.  _Key exchange:_ The method by which crypto keys are shared between sender and receiver.

In cryptography, we start with the unencrypted data, referred to as _plaintext_. Plaintext is _encrypted_ into _ciphertext_, which will in turn (usually) be _decrypted_ back into usable plaintext. The encryption and decryption is based upon the type of cryptography scheme being employed and some form of key. For those who like formulas, this process is sometimes written as:

C = E<sub>k</sub>(P)  
P = D<sub>k</sub>(C)

      where **P** = plaintext, **C** = ciphertext, **E** = the encryption method, **D** = the decryption method, and **k** = the key.

Given this, there are other functions that might be supported by crypto and other terms that one might hear:

-   _Forward Secrecy_ (aka _Perfect Forward Secrecy_): This feature protects past encrypted sessions from compromise even if the server holding the messages is compromised. This is accomplished by creating a different key for every session so that compromise of a single key does not threaten the entirely of the communications.
-   _Perfect Security_: A system that is unbreakable and where the ciphertext conveys no information about the plaintext or the key. To achieve perfect security, the key has to be at least as long as the plaintext, making analysis and even brute-force attacks impossible. One-time pads are an example of such a system.
-   _Deniable Authentication_ (aka _Message Repudiation_): A method whereby participants in an exchange of messages can be assured in the authenticity of the messages but in such a way that senders can later plausibly deny their participation to a third-party.

In many of the descriptions below, two communicating parties will be referred to as Alice and Bob; this is the common nomenclature in the crypto field and literature to make it easier to identify the communicating parties. If there is a third and fourth party to the communication, they will be referred to as Carol and Dave, respectively. A malicious party is referred to as Mallory, an eavesdropper as Eve, and a trusted third party as Trent.

Finally, _cryptography_ is most closely associated with the development and creation of the mathematical algorithms used to encrypt and decrypt messages, whereas _cryptanalysis_ is the science of analyzing and breaking encryption schemes. _Cryptology_ is the umbrella term referring to the broad study of secret writing, and encompasses both cryptography and cryptanalysis.

### TYPES OF CRYPTOGRAPHIC ALGORITHMS

There are several ways of classifying cryptographic algorithms. For purposes of this paper, they will be categorized based on the number of keys that are employed for encryption and decryption, and further defined by their application and use. The three types of algorithms that will be discussed are (Figure 1):

-   _Secret Key Cryptography (SKC):_ Uses a single key for both encryption and decryption; also called _symmetric encryption_. Primarily used for privacy and confidentiality.
-   _Public Key Cryptography (PKC):_ Uses one key for encryption and another for decryption; also called _asymmetric encryption_. Primarily used for authentication, non-repudiation, and key exchange.
-   _Hash Functions:_ Uses a mathematical transformation to irreversibly "encrypt" information, providing a digital fingerprint. Primarily used for message integrity.

![](https://www.garykessler.net/library/images/crypto_types.gif)

**FIGURE 1: Three types of cryptography: secret key, public key, and hash function.**

#### Secret Key Cryptography

Secret key cryptography methods employ a single key for both encryption and decryption. As shown in Figure 1A, the sender uses the key to encrypt the plaintext and sends the ciphertext to the receiver. The receiver applies the same key to decrypt the message and recover the plaintext. Because a single key is used for both functions, secret key cryptography is also called _symmetric encryption_.

With this form of cryptography, it is obvious that the key must be known to both the sender and the receiver; that, in fact, is the secret. The biggest difficulty with this approach, of course, is the distribution of the key (more on that later in the discussion of public key cryptography).

Secret key cryptography schemes are generally categorized as being either _stream ciphers_ or _block ciphers_.

![](https://www.garykessler.net/library/images/crypto_Self-SynchStreamCipher.jpg)

A) Self-synchronizing stream cipher. _(From_ Schneier_, 1996, Figure 9.8)_

![](https://www.garykessler.net/library/images/crypto_SynchStreamCipher.jpg)

B) Synchronous stream cipher. _(From_ Schneier_, 1996, Figure 9.6)_

**FIGURE 2: Types of stream ciphers.**

Stream ciphers operate on a single bit (byte or computer word) at a time and implement some form of feedback mechanism so that the key is constantly changing. Stream ciphers come in several flavors but two are worth mentioning here (Figure 2). _Self-synchronizing stream ciphers_ calculate each bit in the keystream as a function of the previous _n_ bits in the keystream. It is termed "self-synchronizing" because the decryption process can stay synchronized with the encryption process merely by knowing how far into the _n_\-bit keystream it is. One problem is error propagation; a garbled bit in transmission will result in _n_ garbled bits at the receiving side. _Synchronous stream ciphers_ generate the keystream in a fashion independent of the message stream but by using the same keystream generation function at sender and receiver. While stream ciphers do not propagate transmission errors, they are, by their nature, periodic so that the keystream will eventually repeat.

![](https://www.garykessler.net/library/images/crypto_Feistel_cipher_diagram_en.svg.png)

**FIGURE 3: Feistel cipher. _(Source: Wikimedia Commons)_**

A block cipher is so-called because the scheme encrypts one fixed-size block of data at a time. In a block cipher, a given plaintext block will always encrypt to the same ciphertext when using the same key (i.e., it is deterministic) whereas the same plaintext will encrypt to different ciphertext in a stream cipher. The most common construct for block encryption algorithms is the [Feistel cipher](https://en.wikipedia.org/wiki/Feistel_cipher), named for cryptographer Horst Feistel (IBM). As shown in Figure 3, a Feistel cipher combines elements of substitution, permutation (transposition), and key expansion; these features create a large amount of "[confusion and diffusion](https://simple.wikipedia.org/wiki/Confusion_and_diffusion)" (per Claude Shannon) in the cipher. One advantage of the Feistel design is that the encryption and decryption stages are similar, sometimes identical, requiring only a reversal of the key operation, thus dramatically reducing the size of the code or circuitry necessary to implement the cipher in software or hardware, respectively. One of Feistel's early papers describing this operation is "[Cryptography and Computer Privacy](https://www.apprendre-en-ligne.net/crypto/bibliotheque/feistel/)" (_Scientific American_, May 1973, _228_(5), 15-23).

Block ciphers can operate in one of several modes; the following are the most important:

-   _Electronic Codebook (ECB) mode_ is the simplest, most obvious application: the secret key is used to encrypt the plaintext block to form a ciphertext block. Two identical plaintext blocks, then, will always generate the same ciphertext block. ECB is susceptible to a variety of brute-force attacks (because of the fact that the same plaintext block will always encrypt to the same ciphertext), as well as deletion and insertion attacks. In addition, a single bit error in the transmission of the ciphertext results in an error in the entire block of decrypted plaintext.
-   _Cipher Block Chaining (CBC) mode_ adds a feedback mechanism to the encryption scheme; the plaintext is exclusively-ORed (XORed) with the previous ciphertext block prior to encryption so that two identical plaintext blocks will encrypt differently. While CBC protects against many brute-force, deletion, and insertion attacks, a single bit error in the ciphertext yields an entire block error in the decrypted plaintext block _and_ a bit error in the next decrypted plaintext block.
-   _Cipher Feedback (CFB) mode_ is a block cipher implementation as a self-synchronizing stream cipher. CFB mode allows data to be encrypted in units smaller than the block size, which might be useful in some applications such as encrypting interactive terminal input. If we were using one-byte CFB mode, for example, each incoming character is placed into a shift register the same size as the block, encrypted, and the block transmitted. At the receiving side, the ciphertext is decrypted and the extra bits in the block (i.e., everything above and beyond the one byte) are discarded. CFB mode generates a keystream based upon the previous ciphertext (the initial key comes from an Initialization Vector \[IV\]). In this mode, a single bit error in the ciphertext affects both this block and the following one.
-   _Output Feedback (OFB) mode_ is a block cipher implementation conceptually similar to a synchronous stream cipher. OFB prevents the same plaintext block from generating the same ciphertext block by using an internal feedback mechanism that generates the keystream independently of both the plaintext and ciphertext bitstreams. In OFB, a single bit error in ciphertext yields a single bit error in the decrypted plaintext.
-   _Counter (CTR) mode_ is a relatively modern addition to block ciphers. Like CFB and OFB, CTR mode operates on the blocks as in a stream cipher; like ECB, CTR mode operates on the blocks independently. Unlike ECB, however, CTR uses different key inputs to different blocks so that two identical blocks of plaintext will not result in the same ciphertext. Finally, each block of ciphertext has specific location within the encrypted message. CTR mode, then, allows blocks to be processed in parallel — thus offering performance advantages when parallel processing and multiple processors are available — but is not susceptible to ECB's brute-force, deletion, and insertion attacks.

A good overview of these different modes can be found at [CRYPTO-IT](https://www.crypto-it.net/eng/theory/modes-of-block-ciphers.html).

Secret key cryptography algorithms in use today — or, at least, important today even if not in use — include:

-   _Data Encryption Standard (DES):_ One of the most well-known and well-studied SKC schemes, DES was designed by IBM in the 1970s and adopted by the National Bureau of Standards (NBS) \[now the National Institute of Standards and Technology (NIST)\] in 1977 for commercial and unclassified government applications. DES is a Feistel block-cipher employing a 56-bit key that operates on 64-bit blocks. DES has a complex set of rules and transformations that were designed specifically to yield fast hardware implementations and slow software implementations, although this latter point is not significant today since the speed of computer processors is several orders of magnitude faster today than even twenty years ago. DES was based somewhat on an earlier cipher from Feistel called [_Lucifer_](https://en.wikipedia.org/wiki/Lucifer_(cipher)) which, some sources report, had a 112-bit key. This was rejected, partially in order to fit the algorithm onto a single chip and partially because of the National Security Agency (NSA). The NSA also proposed a number of tweaks to DES that many thought were introduced in order to weaken the cipher; analysis in the 1990s, however, showed that the NSA suggestions actually strengthened DES, including the removal of a mathematical back door by a change to the design of the S-box (see "[The Legacy of DES](https://www.schneier.com/blog/archives/2004/10/the_legacy_of_d.html)" by Bruce Schneier \[2004\]). In April 2021, the NSA declassified a fascinating historical paper titled ["NSA Comes Out of the Closet: The Debate over Public Cryptography in the Inman Era"](https://cryptome.org/2021/04/Joseph-Meyer-IEEE-1977.pdf) that appeared in _Cryptologic Quarterly_, Spring 1996.
    
    DES was defined in American National Standard X3.92 and three Federal Information Processing Standards (FIPS), all withdrawn in 2005:
    
    -   [FIPS PUB 46-3: DES](http://csrc.nist.gov/publications/fips/fips46-3/fips46-3.pdf) (Archived file)
    -   FIPS PUB 74: Guidelines for Implementing and Using the NBS Data Encryption Standard
    -   FIPS PUB 81: DES Modes of Operation
    
    Information about vulnerabilities of DES can be obtained from the [Electronic Frontier Foundation](https://w2.eff.org/Privacy/Crypto/Crypto_misc/DESCracker/HTML/19980716_eff_des_faq.html).
    
    Two important variants that strengthen DES are:
    
    -   _Triple-DES (3DES):_ A variant of DES that employs up to three 56-bit keys and makes three encryption/decryption passes over the block; 3DES is also described in [FIPS PUB 46-3](http://csrc.nist.gov/publications/fips/fips46-3/fips46-3.pdf) and was an interim replacement to DES in the late-1990s and early-2000s.
        
    -   _[DESX](https://en.wikipedia.org/wiki/DES-X):_ A variant devised by Ron Rivest. By combining 64 additional key bits to the plaintext prior to encryption, effectively increases the keylength to 120 bits.
        
    
    More detail about DES, 3DES, and DESX can be found below in [Section 5.4](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#desmath).
    
-   _Advanced Encryption Standard (AES):_ In 1997, NIST initiated a very public, 4-1/2 year process to develop a new secure cryptosystem for U.S. government applications (as opposed to the very closed process in the adoption of DES 25 years earlier). The result, the [Advanced Encryption Standard](https://csrc.nist.gov/projects/cryptographic-standards-and-guidelines/archived-crypto-projects/aes-development), became the official successor to DES in December 2001. AES uses an SKC scheme called [Rijndael](http://www.efgh.com/software/rijndael.htm), a block cipher designed by Belgian cryptographers Joan Daemen and Vincent Rijmen. The algorithm can use a variable block length and key length; the latest specification allowed any combination of keys lengths of 128, 192, or 256 bits and blocks of length 128, 192, or 256 bits. NIST initially selected Rijndael in October 2000 and formal adoption as the AES standard came in December 2001. [FIPS PUB 197](http://csrc.nist.gov/publications/fips/fips197/fips-197.pdf) describes a 128-bit block cipher employing a 128-, 192-, or 256-bit key. AES is also part of the [NESSIE](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#nessie) approved suite of protocols. (See also the entries for [CRYPTEC](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#cryptec) and [NESSIE](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#nessie) Projects in Table 3.)
    
    The AES process and Rijndael algorithm are described in more detail below in [Section 5.9](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#aes).
    
-   _[CAST-128/256](https://tnlandforms.us/cs594-cns96/cast.pdf):_ CAST-128 (aka CAST5), described in [Request for Comments (RFC) 2144](https://www.rfc-editor.org/info/rfc2144), is a DES-like substitution-permutation crypto algorithm, employing a 128-bit key operating on a 64-bit block. CAST-256 (aka CAST6), described in [RFC 2612](https://www.rfc-editor.org/info/rfc2612), is an extension of CAST-128, using a 128-bit block size and a variable length (128, 160, 192, 224, or 256 bit) key. CAST is named for its developers, Carlisle Adams and Stafford Tavares, and is available internationally. CAST-256 was one of the Round 1 algorithms in the AES process.
    
-   _[International Data Encryption Algorithm (IDEA)](http://www.quadibloc.com/crypto/co040302.htm):_ Secret-key cryptosystem written by Xuejia Lai and James Massey, in 1992 and patented by Ascom; a 64-bit SKC block cipher using a 128-bit key.
    
-   _Rivest Ciphers (_aka _Ron's Code):_ Named for Ron Rivest, a series of SKC algorithms.
    
    -   _RC1:_ Designed on paper but never implemented.
        
    -   _RC2:_ A 64-bit block cipher using variable-sized keys designed to replace DES. It's code has not been made public although many companies have licensed RC2 for use in their products. Described in [RFC 2268](https://www.rfc-editor.org/info/rfc2268).
        
    -   _RC3:_ Found to be breakable during development.
        
    -   _RC4:_ A stream cipher using variable-sized keys; it is widely used in commercial cryptography products. An update to RC4, called [Spritz](https://people.csail.mit.edu/rivest/pubs/RS14.pdf) (see also this [article](https://www.schneier.com/blog/archives/2014/10/spritz_a_new_rc.html)), was designed by Rivest and Jacob Schuldt. More detail about RC4 (and a little about Spritz) can be found below in [Section 5.13](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#rc4).
        
    -   _[RC5](http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/rc5-and-rc6.htm):_ A block-cipher supporting a variety of block sizes (32, 64, or 128 bits), key sizes, and number of encryption passes over the data. Described in [RFC 2040](https://www.rfc-editor.org/info/rfc2040).
        
    -   _[RC6](https://en.wikipedia.org/wiki/RC6):_ A 128-bit block cipher based upon, and an improvement over, RC5; [RC6](http://csrc.nist.gov/archive/aes/round1/conf1/rc6-slides.pdf) was one of the AES Round 2 algorithms.
    
-   _[Blowfish](https://www.schneier.com/cryptography/blowfish/):_ A symmetric 64-bit block cipher invented by Bruce Schneier; optimized for 32-bit processors with large data caches, it is significantly faster than DES on a Pentium/PowerPC-class machine. Key lengths can vary from 32 to 448 bits in length. Blowfish, available freely and intended as a substitute for DES or IDEA, is in use in a large number of products.
    
-   _[Twofish](https://www.schneier.com/cryptography/twofish/):_ A 128-bit block cipher using 128-, 192-, or 256-bit keys. Designed to be highly secure and highly flexible, well-suited for large microprocessors, 8-bit smart card microprocessors, and dedicated hardware. Designed by a team led by Bruce Schneier and was one of the Round 2 algorithms in the AES process.
    
-   _[Threefish](https://www.schneier.com/academic/skein/threefish/):_ A [large block cipher](https://en.wikipedia.org/wiki/Threefish), supporting 256-, 512-, and 1024-bit blocks and a key size that matches the block size; by design, the block/key size can grow in increments of 128 bits. Threefish only uses XOR operations, addition, and rotations of 64-bit words; the design philosophy is that an algorithm employing many computationally simple rounds is more secure than one employing highly complex — albeit fewer — rounds. The specification for Threefish is part of the [Skein Hash Function Family](http://www.skein-hash.info/sites/default/files/skein1.3.pdf) documentation.
    
-   _[Anubis](https://en.wikipedia.org/wiki/Anubis_(cipher)):_ Anubis is a block cipher, co-designed by Vincent Rijmen who was one of the designers of Rijndael. [Anubis](https://www.garykessler.net/library/crypto/Anubis.pdf) is a block cipher, performing substitution-permutation operations on 128-bit blocks and employing keys of length 128 to 3200 bits (in 32-bit increments). Anubis works very much like [Rijndael](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#aes). Although submitted to the [NESSIE](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#nessie) project, it did not make the final cut for inclusion.
    
-   _[ARIA](http://210.104.33.10/ARIA/index-e.html):_ A 128-bit block cipher employing 128-, 192-, and 256-bit keys to encrypt 128-bit blocks in 12, 14, and 16 rounds, depending on the key size. Developed by large group of researchers from academic institutions, research institutes, and federal agencies in South Korea in 2003, and subsequently named a national standard. Described in [RFC 5794](https://www.rfc-editor.org/info/rfc5794).
    
-   _[Camellia](http://www.cryptrec.go.jp/english/cryptrec_03_spec_cypherlist_files/PDF/06_01espec.pdf):_ A secret-key, block-cipher crypto algorithm developed jointly by Nippon Telegraph and Telephone (NTT) Corp. and Mitsubishi Electric Corporation (MEC) in 2000. Camellia has some characteristics in common with AES: a 128-bit block size, support for 128-, 192-, and 256-bit key lengths, and suitability for both software and hardware implementations on common 32-bit processors as well as 8-bit processors (e.g., smart cards, cryptographic hardware, and embedded systems). Also described in [RFC 3713](https://www.rfc-editor.org/info/rfc3713). Camellia's application in IPsec is described in [RFC 4312](https://www.rfc-editor.org/info/rfc4312) and application in OpenPGP in [RFC 9580](https://www.rfc-editor.org/info/rfc9580). Camellia is part of the [NESSIE](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#nessie) suite of protocols.
    
-   _[CLEFIA](http://www.sony.net/Products/cryptography/clefia/):_ Described in [RFC 6114](https://www.rfc-editor.org/info/rfc6114), CLEFIA is a 128-bit block cipher employing key lengths of 128, 192, and 256 bits (which is compatible with AES). The [CLEFIA algorithm](http://www.sony.net/Products/cryptography/clefia/about/index.html) was first published in 2007 by Sony Corporation. CLEFIA is one of the new-generation lightweight block cipher algorithms designed after AES, offering high performance in software and hardware as well as a lightweight implementation in hardware.
    
-   _FFX-A2_ and _FFX-A10_: FFX (Format-preserving, Feistel-based encryption) is a type of Format Preserving Encryption (FPE) scheme that is designed so that the ciphertext has the same format as the plaintext. FPE schemes are used for such purposes as encrypting social security numbers, credit card numbers, limited size protocol traffic, etc.; this means that an encrypted social security number, for example, would still be a nine-digit string. FFX can theoretically encrypt strings of arbitrary length, although it is intended for message sizes smaller than that of AES-128 (2<sup>128</sup> points). The [FFX version 1.1 specification](http://csrc.nist.gov/groups/ST/toolkit/BCM/documents/proposedmodes/ffx/ffx-spec.pdf) describes FFX-A2 and FFX-A10, which are intended for 8-128 bit binary strings or 4-36 digit decimal strings.
    
-   _GSM (Global System for Mobile Communications, originally Groupe Spécial Mobile) encryption:_ GSM mobile phone systems use [several stream ciphers](http://ipsec.pl/files/ipsec/security_in_the_gsm_network.pdf) for over-the-air communication privacy. [A5/1](https://en.wikipedia.org/wiki/A5/1) was developed in 1987 for use in Europe and the U.S. [A5/2](https://en.wikipedia.org/wiki/A5/2), developed in 1989, is a weaker algorithm and intended for use outside of Europe and the U.S. Significant flaws were found in both ciphers after the "secret" specifications were leaked in 1994, however, and A5/2 has been withdrawn from use. The newest version, A5/3, employs the KASUMI block cipher. **NOTE:** Unfortunately, although A5/1 has been repeatedly "broken" (e.g., see ["Secret code protecting cellphone calls set loose"](http://www.theregister.co.uk/2009/12/28/gsm_eavesdropping_breakthrough/) \[2009\] and ["Cellphone snooping now easier and cheaper than ever"](http://www.theregister.co.uk/2011/01/03/gsm_eavesdropping/) \[2011\]), this encryption scheme remains in widespread use, even in 3G and 4G mobile phone networks. Use of this scheme is reportedly one of the reasons that the National Security Agency (NSA) can easily decode voice and data calls over mobile phone networks.
    
-   _GPRS (General Packet Radio Service) encryption:_ GSM mobile phone systems use [GPRS](https://en.wikipedia.org/wiki/General_Packet_Radio_Service) for data applications, and GPRS uses a number of [encryption methods](http://events.ccc.de/camp/2011/Fahrplan/attachments/1868_110810.SRLabs-Camp-GRPS_Intercept.pdf), offering different levels of data protection. GEA/0 offers no encryption at all. GEA/1 and GEA/2 are proprietary stream ciphers, employing a 64-bit key and a 96-bit or 128-bit state, respectively. GEA/1 and GEA/2 are most widely used by network service providers today although both have been reportedly broken. GEA/3 is a 128-bit block cipher employing a 64-bit key that is used by some carriers; GEA/4 is a 128-bit clock cipher with a 128-bit key, but is not yet deployed.
    
-   _[KASUMI](https://www.garykessler.net/library/crypto/3G_KASUMI.pdf):_ A block cipher using a 128-bit key that is part of the Third-Generation Partnership Project (3gpp), formerly known as the Universal Mobile Telecommunications System (UMTS). KASUMI is the intended confidentiality and integrity algorithm for both message content and signaling data for emerging mobile communications systems.
    
-   _KCipher-2:_ Described in [RFC 7008](https://www.rfc-editor.org/info/rfc7008), KCipher-2 is a stream cipher with a 128-bit key and a 128-bit initialization vector. Using simple arithmetic operations, the algorithms offers fast encryption and decryption by use of efficient implementations. KCipher-2 has been used for industrial applications, especially for mobile health monitoring and diagnostic services in Japan.
    
-   _[KHAZAD](https://en.wikipedia.org/wiki/KHAZAD):_ [KHAZAD](https://www.garykessler.net/library/crypto/Khazad.pdf) is a so-called _legacy_ block cipher, operating on 64-bit blocks à la older block ciphers such as DES and IDEA. KHAZAD uses eight rounds of substitution and permutation, with a 128-bit key.
    
-   _[KLEIN](http://rfid-cusp.org/rfidsec/files/RFIDSec2011DraftPapers/GongEtAl.pdf):_ Designed in 2011, KLEIN is a lightweight, 64-bit block cipher supporting 64-, 80- and 96-bit keys. KLEIN is designed for highly resource constrained devices such as wireless sensors and RFID tags.
    
-   _[Light Encryption Device (LED)](https://eprint.iacr.org/2012/600.pdf)_: Designed in 2011, LED is a lightweight, 64-bit block cipher supporting 64- and 128-bit keys. LED is designed for RFID tags, sensor networks, and other applications with devices constrained by memory or compute power.
    
-   _[MARS](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.35.6084&rep=rep1&type=pdf):_ [MARS](https://en.wikipedia.org/wiki/MARS_(cipher)) is a block cipher developed by IBM and was one of the five finalists in the AES development process. MARS employs 128-bit blocks and a variable key length from 128 to 448 bits. The MARS document stresses the ability of the algorithm's design for high speed, high security, and the ability to efficiently and effectively implement the scheme on a wide range of computing devices.
    
-   _[MISTY1](https://en.wikipedia.org/wiki/MISTY1):_ Developed at Mitsubishi Electric Corp., a block cipher using a 128-bit key and 64-bit blocks, and a variable number of rounds. Designed for hardware and software implementations, and is resistant to differential and linear cryptanalysis. Described in [RFC 2994](https://www.rfc-editor.org/info/rfc2994), MISTY1 is part of the [NESSIE](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#nessie) suite.
    
-   _Salsa_ and _ChaCha_: [Salsa20](https://en.wikipedia.org/wiki/Salsa20) is a stream cipher proposed for the [eSTREAM](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#estream) project by Daniel Bernstein. Salsa20 uses a pseudorandom function based on 32-bit (whole word) addition, bitwise addition (XOR), and rotation operations, aka _add-rotate-xor (ARX)_ operations. Salsa20 uses a 256-bit key although a 128-bit key variant also exists. In 2008, Bernstein published [ChaCha](http://cr.yp.to/chacha/chacha-20080128.pdf), a new family of ciphers related to Salsa20. ChaCha20, originally defined in [RFC 7539](https://tools.ietf.org/html/rfc7539) (now obsoleted), is employed (with the [Poly1305 authenticator](http://cr.yp.to/mac/poly1305-20050329.pdf)) in Internet Engineering Task Force (IETF) protocols, most notably for IPsec and Internet Key Exchange (IKE), per [RFC 7634](https://tools.ietf.org/html/rfc7634), and Transaction Layer Security (TLS), per [RFC 7905](https://tools.ietf.org/html/rfc7905). In 2014, Google adopted ChaCha20/Poly1305 for use in OpenSSL, and they are also a part of OpenSSH. [RFC 8439](https://tools.ietf.org/html/rfc8439) replaces RFC 7539, and provides an implementation guide for both the ChaCha20 cipher and Poly1305 message authentication code, as well as the combined CHACHA20-POLY1305 [Authenticated-Encryption with Associated-Data (AEAD)](https://web.cs.ucdavis.edu/~rogaway/papers/ad.html) algorithm.
    
-   _[Secure and Fast Encryption Routine (SAFER)](http://www.quadibloc.com/crypto/co040301.htm):_ A series of block ciphers designed by James Massey for implementation in software and employing a 64-bit block. SAFER K-64, published in 1993, used a 64-bit key and SAFER K-128, published in 1994, employed a 128-bit key. After weaknesses were found, new versions were released called SAFER SK-40, SK-64, and SK-128, using 40-, 64-, and 128-bit keys, respectively. SAFER+ (1998) used a 128-bit block and was an unsuccessful candidate for the AES project; SAFER++ (2000) was submitted to the [NESSIE](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#nessie) project.
    
-   _[SEED](https://en.wikipedia.org/wiki/SEED):_ A block cipher using 128-bit blocks and 128-bit keys. Developed by the Korea Information Security Agency (KISA) and adopted as a national standard encryption algorithm in South Korea. Also described in [RFC 4269](https://www.rfc-editor.org/info/rfc4269).
    
-   _[Serpent](https://en.wikipedia.org/wiki/Serpent_(cipher)):_ [Serpent](https://www.cs.technion.ac.il/~biham/Reports/Serpent/) is another of the AES finalist algorithms. [Serpent](https://www.cl.cam.ac.uk/~rja14/serpent.html) supports 128-, 192-, or 256-bit keys and a block size of 128 bits, and is a 32-round substitutionâ€“permutation network operating on a block of four 32-bit words. The Serpent developers opted for a high security margin in the design of the algorithm; they determined that 16 rounds would be sufficient against known attacks but require 32 rounds in an attempt to future-proof the algorithm.
    
-   _[SHACAL](https://en.m.wikipedia.org/wiki/SHACAL):_ SHACAL is a pair of block ciphers based upon the Secure Hash Algorithm (SHA) and the fact that SHA is, at heart, a compression algorithm. As a hash function, SHA repeatedly calls on a compression scheme to alter the state of the data blocks. While SHA (like other hash functions) is irreversible, the compression function can be used for encryption by maintaining appropriate state information. SHACAL-1 is based upon SHA-1 and uses a 160-bit block size while [SHACAL-2](https://github.com/odzhan/tinycrypt/blob/master/block/shacal2/doc/10.1.1.3.4066.pdf) is based upon SHA-256 and employs a 256-bit block size; both support key sizes from 128 to 512 bits. SHACAL-2 is one of the [NESSIE](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#nessie) block ciphers.
    
-   _Simon_ and _Speck_: [Simon and Speck](https://eprint.iacr.org/2013/404.pdf) are a pair of lightweight block ciphers proposed by the NSA in 2013, designed for highly constrained software or hardware environments. (E.g., per the specification, AES requires 2400 gate equivalents and these ciphers require less than 2000.) While both cipher families perform well in both hardware and software, Simon has been optimized for high performance on hardware devices and Speck for performance in software. Both are Feistel ciphers and support ten combinations of block and key size:
    
-   _[Skipjack](http://csrc.nist.gov/groups/ST/toolkit/documents/skipjack/skipjack.pdf):_ SKC scheme proposed, along with the [Clipper chip](http://www.cryptomuseum.com/crypto/usa/clipper.htm), as part of the never-implemented Capstone project. Although the details of the algorithm were never made public, [Skipjack](http://www.cryptomuseum.com/crypto/usa/skipjack.htm) was a block cipher using an 80-bit key and 32 iteration cycles per 64-bit block. Capstone, proposed by NIST and the NSA as a standard for public and government use, met with great resistance by the crypto community largely because the design of Skipjack was classified (coupled with the key escrow requirement of the Clipper chip).
    
-   _SM4:_ Formerly called SMS4, SM4 is a 128-bit block cipher using 128-bit keys and 32 rounds to process a block. Declassified in 2006, SM4 is used in the Chinese National Standard for Wireless Local Area Network (LAN) Authentication and Privacy Infrastructure (WAPI). SM4 had been a proposed cipher for the Institute of Electrical and Electronics Engineers (IEEE) 802.11i standard on security mechanisms for wireless LANs, but has yet to be accepted by the IEEE or International Organization for Standardization (ISO). SM4 is described in [_SMS4 Encryption Algorithm for Wireless Networks_](http://eprint.iacr.org/2008/329.pdf) (translated by Whitfield Diffie and George Ledin, 2008) and at the [SM4 (cipher)](https://en.wikipedia.org/wiki/SM4_(cipher)) page. SM4 is issued by the Chinese State Cryptographic Authority as _GM/T 0002-2012: SM4_ (2012).
    
-   _Tiny Encryption Algorithm (TEA):_ A family of block ciphers developed by Roger Needham and David Wheeler. [TEA](https://en.wikipedia.org/wiki/Tiny_Encryption_Algorithm) was originally developed in 1994, and employed a 128-bit key, 64-bit block, and 64 rounds of operation. To correct certain weaknesses in TEA, [eXtended TEA (XTEA)](https://en.wikipedia.org/wiki/XTEA), aka Block TEA, was released in 1997. To correct weaknesses in XTEA and add versatility, [Corrected Block TEA (XXTEA)](https://en.wikipedia.org/wiki/XXTEA) was published in 1998. XXTEA also uses a 128-bit key, but block size can be any multiple of 32-bit words (with a minimum block size of 64 bits, or two words) and the number of rounds is a function of the block size (~52+6\*words), as shown in Table 1.
    

<table><caption><b>TABLE 1. Tiny Encryption Algorithm (TEA) options.</b></caption><tbody><tr><th>Block Size<br><i>2n</i></th><th>Key Size<br><i>mn</i></th><th>Word Size<br><i>n</i></th><th>Key Words<br><i>m</i></th><th>Rounds<br><i>T</i></th></tr><tr><td>32</td><td>64</td><td>16</td><td>4</td><td>32</td></tr><tr><td>48</td><td>72<br>96</td><td>24</td><td>3<br>4</td><td>36<br>36</td></tr><tr><td>64</td><td>96<br>128</td><td>32</td><td>3<br>4</td><td>42<br>44</td></tr><tr><td>96</td><td>96<br>144</td><td>48</td><td>2<br>3</td><td>52<br>54</td></tr><tr><td>128</td><td>128<br>192<br>256</td><td>64</td><td>2<br>3<br>4</td><td>68<br>69<br>72</td></tr></tbody></table>

-   _[TWINE](https://jpn.nec.com/rd/tg/code/research/image/twine_SAC_full_v5.pdf)_: Designed by engineers at NEC in 2011, TWINE is a lightweight, 64-bit block cipher supporting 80- and 128-bit keys. TWINE's design goals included maintaining a small footprint in a hardware implementation (i.e., fewer than 2,000 gate equivalents) and small memory consumption in a software implementation.
    

Although not an SKC scheme, check out [Section 5.17](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#sss) about Shamir's Secret Sharing (SSS).

There are several other references that describe interesting algorithms and even SKC codes dating back decades. Two that leap to mind are the Crypto Museum's [Crypto List](http://www.cryptomuseum.com/crypto/list.htm) and John J.G. Savard's (albeit old) [A Cryptographic Compendium](http://www.quadibloc.com/crypto/jscrypt.htm) page.

#### Public Key Cryptography

Public key cryptography has been said to be the most significant new development in cryptography in the last 300-400 years. Modern PKC was first described publicly by Stanford University professor Martin Hellman and graduate student Whitfield Diffie in 1976. Their paper described a two-key crypto system in which two parties could engage in a secure communication over a non-secure communications channel without having to share a secret key.

PKC depends upon the existence of so-called _one-way functions_, or mathematical functions that are easy to compute whereas their inverse function is relatively difficult to compute. Let me give you two simple examples:

1.  _Multiplication vs. factorization:_ Suppose you have two prime numbers, 3 and 7, and you need to calculate the product; it should take almost no time to calculate that value, which is 21. Now suppose, instead, that you have a number that is a product of two primes, 21, and you need to determine those prime factors. You will eventually come up with the solution but whereas calculating the product took milliseconds, factoring will take longer. The problem becomes much harder if we start with primes that have, say, 400 digits or so, because the product will have ~800 digits.
2.  _Exponentiation vs. logarithms:_ Suppose you take the number 3 to the 6th power; again, it is relatively easy to calculate 3<sup>6</sup> = 729. But if you start with the number 729 and need to determine the two integers, _x_ and _y_ so that log<sub>x</sub> 729 = y, it will take longer to find the two values.

While the examples above are trivial, they do represent two of the functional pairs that are used with PKC; namely, the ease of multiplication and exponentiation versus the relative difficulty of factoring and calculating logarithms, respectively. The mathematical "trick" in PKC is to find a _trap door_ in the one-way function so that the inverse calculation becomes easy given knowledge of some item of information.

Generic PKC employs two keys that are mathematically related although knowledge of one key does not allow someone to easily determine the other key. One key is used to encrypt the plaintext and the other key is used to decrypt the ciphertext. The important point here is that it **does not matter which key is applied first**, but that both keys are required for the process to work (Figure 1B). Because a pair of keys are required, this approach is also called _asymmetric cryptography_.

In PKC, one of the keys is designated the _public key_ and may be advertised as widely as the owner wants. The other key is designated the _private key_ and is never revealed to another party. It is straight-forward to send messages under this scheme. Suppose Alice wants to send Bob a message. Alice encrypts some information using Bob's public key; Bob decrypts the ciphertext using his private key. This method could be also used to prove who sent a message; Alice, for example, could encrypt some plaintext with her private key; when Bob decrypts using Alice's public key, he knows that Alice sent the message (_authentication_) and Alice cannot deny having sent the message (_non-repudiation_).

Public key cryptography algorithms that are in use today for key exchange or digital signatures include:

-   _RSA:_ The first, and still most common, PKC implementation, named for the three MIT mathematicians who developed it — Ronald Rivest, Adi Shamir, and Leonard Adleman. RSA today is used in hundreds of software products and can be used for key exchange, digital signatures, or encryption of small blocks of data. RSA uses a variable size encryption block and a variable size key. The key-pair is derived from a very large number, _n_, that is the product of two prime numbers chosen according to special rules; these primes may be 100 or more digits in length each, yielding an _n_ with roughly twice as many digits as the prime factors. The public key information includes _n_ and a derivative of one of the factors of _n_; an attacker cannot determine the prime factors of _n_ (and, therefore, the private key) from this information alone and that is what makes the RSA algorithm so secure. (Some descriptions of PKC erroneously state that RSA's safety is due to the difficulty in _factoring_ large prime numbers. In fact, large prime numbers, like small prime numbers, only have two factors!) The ability for computers to factor large numbers, and therefore attack schemes such as RSA, is rapidly improving and systems today can find the prime factors of numbers with more than 200 digits. Nevertheless, if a large number is created from two prime factors that are roughly the same size, there is no known factorization algorithm that will solve the problem in a reasonable amount of time; a 2005 test to factor a 200-digit number took 1.5 years and over 50 years of compute time. In 2009, [Kleinjung et al.](http://eprint.iacr.org/2010/006.pdf) reported that factoring a 768-bit (232-digit) RSA-768 modulus utilizing hundreds of systems took two years and they estimated that a 1024-bit RSA modulus would take about a thousand times as long. Even so, they suggested that 1024-bit RSA be phased out by 2013. (See the Wikipedia article on [integer factorization](https://en.wikipedia.org/wiki/Integer_factorization).) Regardless, one presumed protection of RSA is that users can easily increase the key size to always stay ahead of the computer processing curve. As an aside, the patent for RSA expired in September 2000 which does not appear to have affected RSA's popularity one way or the other. A detailed example of RSA is presented below in [Section 5.3](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#rsamath).
    
-   _[Diffie-Hellman](http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/what-is-diffie-hellman.htm):_ After the RSA algorithm was published, Diffie and Hellman came up with their own algorithm. Diffie-Hellman is used for secret-key key exchange only, and not for authentication or digital signatures. More detail about Diffie-Hellman can be found below in [Section 5.2](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#dhmath).
    
-   _[Digital Signature Algorithm (DSA)](https://en.wikipedia.org/wiki/Digital_Signature_Algorithm):_ The algorithm specified in NIST's [Digital Signature Standard (DSS)](http://csrc.nist.gov/groups/ST/toolkit/digital_signatures.html), provides digital signature capability for the authentication of messages. Described in [FIPS PUB 186-4](http://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
    
-   _[ElGamal](http://www.iusmentis.com/technology/encryption/elgamal/):_ Designed by Taher Elgamal, [ElGamal](https://en.wikipedia.org/wiki/ElGamal_encryption) is a PKC system similar to Diffie-Hellman and used for key exchange. ElGamal is used in some later version of Pretty Good Privacy (PGP) as well as GNU Privacy Guard (GPG) and other cryptosystems.
    
-   _Elliptic Curve Cryptography (ECC):_ A PKC algorithm based upon elliptic curves. ECC can offer levels of security with small keys comparable to RSA and other PKC methods. It was designed for devices with limited compute power and/or memory, such as smartcards and PDAs. More detail about ECC can be found below in [Section 5.8](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#ecc). Other references include the [Elliptic Curve Cryptography](https://www.certicom.com/ecc) page and the [Online ECC Tutorial](https://www.certicom.com/ecc-tutorial) page, both from Certicom. See also [RFC 6090](https://www.rfc-editor.org/info/rfc6090) for a review of fundamental ECC algorithms and [_The Elliptic Curve Digital Signature Algorithm (ECDSA)_](http://www.cs.miami.edu/home/burt/learning/Csc609.142/ecdsa-cert.pdf) for details about the use of ECC for digital signatures.
    
-   _[Identity-Based Encryption (IBE)](https://crypto.stanford.edu/ibe/):_ IBE is a novel scheme first proposed by Adi Shamir in 1984. It is a PKC-based key authentication system where the public key can be derived from some unique information based upon the user's identity, allowing two users to exchange encrypted messages without having an _a priori_ relationship. In 2001, Dan Boneh (Stanford) and Matt Franklin (U.C., Davis) developed a practical implementation of IBE based on elliptic curves and a mathematical construct called the [Weil Pairing](https://crypto.stanford.edu/~dabo/papers/bfibe.pdf). In that year, Clifford Cocks (GCHQ) also described another IBE solution based on quadratic residues in composite groups. [RFC 5091: Identity-Based Cryptography Standard (IBCS) #1](https://www.rfc-editor.org/info/rfc5091) describes an implementation of IBE using Boneh-Franklin (BF) and Boneh-Boyen (BB1) Identity-based Encryption. More detail about Identity-Based Encryption can be found below in [Section 5.16](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#ibe).
    
-   _[Public Key Cryptography Standards (PKCS)](https://web.archive.org/web/20141026155838/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/public-key-cryptography-standards.htm):_ A set of interoperable standards and guidelines for public key cryptography, designed by RSA Data Security Inc. (These documents are no longer easily available; all links in this section are from _archive.org_.)
    
-   [PKCS #1](https://web.archive.org/web/20141026161629/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/pkcs-rsa-cryptography-standard.htm): RSA Cryptography Standard (Also [RFC 8017](https://www.rfc-editor.org/info/rfc8017))
-   PKCS #2: _Incorporated into PKCS #1._
-   [PKCS #3](https://web.archive.org/web/20141026155807/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/pkcs-3-diffie-hellman-key-agreement-standar.htm): [Diffie-Hellman Key-Agreement Standard](https://www.teletrust.de/fileadmin/files/oid/oid_pkcs-3v1-4.pdf)
-   PKCS #4: _Incorporated into PKCS #1._
-   [PKCS #5](https://web.archive.org/web/20141026154207/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/pkcs-5-password-based-cryptography-standard.htm): Password-Based Cryptography Standard (PKCS #5 V2.1 is also [RFC 8018](https://www.rfc-editor.org/info/rfc8018))
-   [PKCS #6](https://web.archive.org/web/20141026155041/http://www.emc.com/emc-plus/rsa-labs/standars-initiatives/pkcs-6-extended-certificate-syntax-standard.htm): Extended-Certificate Syntax Standard (being phased out in favor of X.509v3)
-   [PKCS #7](https://web.archive.org/web/20141026160604/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/pkcs-7-cryptographic-message-syntax-standar.htm): Cryptographic Message Syntax Standard (Also [RFC 2315](https://www.rfc-editor.org/info/rfc2315))
-   [PKCS #8](https://web.archive.org/web/20141026160609/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/pkcs-8-private-key-information-syntax-stand.htm): Private-Key Information Syntax Standard (Also [RFC 5958](https://www.rfc-editor.org/info/rfc5958))
-   [PKCS #9](https://web.archive.org/web/20141026155144/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/pkcs-9-selected-attribute-types.htm): Selected Attribute Types (Also [RFC 2985](https://www.rfc-editor.org/info/rfc2985))
-   [PKCS #10](https://web.archive.org/web/20141026155308/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/pkcs10-certification-request-syntax-standard.htm): Certification Request Syntax Standard (Also [RFC 2986](https://www.rfc-editor.org/info/rfc2986))
-   [PKCS #11](https://web.archive.org/web/20141026160503/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/pkcs-11-cryptographic-token-interface-standard.htm): Cryptographic Token Interface Standard
-   [PKCS #12](https://web.archive.org/web/20140401120450/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/pkcs12-personal-information-exchange-syntax-standard.htm): Personal Information Exchange Syntax Standard (Also [RFC 7292](https://www.rfc-editor.org/info/rfc7292))
-   [PKCS #13](https://web.archive.org/web/20141026155921/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/pkcs-13-elliptic-curve-cryptography-standard.htm): Elliptic Curve Cryptography Standard
-   PKCS #14: _Pseudorandom Number Generation Standard is no longer available_
-   [PKCS #15](https://web.archive.org/web/20141026161516/http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/pkcs-15-cryptographic-token-information-format.htm): Cryptographic Token Information Format Standard

-   _[Cramer-Shoup](https://homepages.cwi.nl/~schaffne/courses/crypto/2014/presentations/Eileen_CramerShoup.pdf):_ A public key cryptosystem proposed by R. Cramer and V. Shoup of IBM in 1998.
    
-   _[Key Exchange Algorithm (KEA)](http://csrc.nist.gov/groups/ST/toolkit/documents/skipjack/skipjack.pdf):_ A variation on Diffie-Hellman; proposed as the key exchange method for the NIST/NSA Capstone project.
    
-   _[LUC](https://www.cryptopp.com/wiki/LUC_Cryptography):_ A public key cryptosystem designed by P.J. Smith and based on Lucas sequences. Can be used for encryption and signatures, using integer factoring.
    
-   _[McEliece](http://ipnpr.jpl.nasa.gov/progress_report2/42-44/44N.PDF):_ A public key cryptosystem based on algebraic coding theory.
    

For additional information on PKC algorithms, see ["Public Key Encryption"](https://www.garykessler.net/library/crypto/hac_chap08.pdf) (Chapter 8) in _Handbook of Applied Cryptography_, by A. Menezes, P. van Oorschot, and S. Vanstone (CRC Press, 1996).

___

> **A digression: Who invented PKC?** I tried to be careful in the first paragraph of this section to state that Diffie and Hellman "first described publicly" a PKC scheme. Although I have categorized PKC as a two-key system, that has been merely for convenience; the real criteria for a PKC scheme is that it allows two parties to exchange a secret even though the communication with the shared secret might be overheard. There seems to be no question that Diffie and Hellman were first to publish; their method is described in the classic paper, "[New Directions in Cryptography](http://www-ee.stanford.edu/%7Ehellman/publications/24.pdf)," published in the November 1976 issue of _IEEE Transactions on Information Theory_ (_IT-22_(6), 644-654). As shown in [Section 5.2](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#dhmath), Diffie-Hellman uses the idea that finding logarithms is relatively harder than performing exponentiation. And, indeed, it is the precursor to modern PKC which does employ two keys. Rivest, Shamir, and Adleman described an implementation that extended this idea in their paper, "[A Method for Obtaining Digital Signatures and Public Key Cryptosystems](http://people.csail.mit.edu/rivest/Rsapaper.pdf)," published in the February 1978 issue of the _Communications of the ACM (CACM)_, (_21_(2), 120-126). Their method, of course, is based upon the relative ease of finding the product of two large prime numbers compared to finding the prime factors of a large number.
> 
> Diffie and Hellman (and other sources) credit Ralph Merkle with first describing a _public key distribution_ system that allows two parties to share a secret, although it was not a two-key system, per se. A _Merkle Puzzle_ works where Alice creates a large number of encrypted keys, sends them all to Bob so that Bob chooses one at random and then lets Alice know which he has selected. An eavesdropper (Eve) will see all of the keys but can't learn which key Bob has selected (because he has encrypted the response with the chosen key). In this case, Eve's effort to break in is the square of the effort of Bob to choose a key. While this difference may be small it is often sufficient. Merkle apparently took a computer science course at UC Berkeley in 1974 and described his method, but had difficulty making people understand it; frustrated, he dropped the course. Meanwhile, he submitted the paper "[Secure Communication Over Insecure Channels](http://www.merkle.com/1974/PuzzlesAsPublished.pdf)," which was published in the _CACM_ in April 1978; Rivest et al.'s paper even makes reference to it. Merkle's method certainly wasn't published first, but he is often credited to have had the idea first.
> 
> An interesting question, maybe, but who really knows? For some time, it was a quiet secret that a team at the UK's Government Communications Headquarters (GCHQ) had first developed PKC in the early 1970s. Because of the nature of the work, GCHQ kept the original memos classified. In 1997, however, the GCHQ changed their posture when they realized that there was nothing to gain by continued silence. Documents show that a GCHQ mathematician named James Ellis started research into the key distribution problem in 1969 and that by 1975, James Ellis, Clifford Cocks, and Malcolm Williamson had worked out all of the fundamental details of PKC, yet couldn't talk about their work. (They were, of course, barred from challenging the RSA patent!) By 1999, Ellis, Cocks, and Williamson began to get their due credit in a break-through article in _[WIRED Magazine](http://www.wired.com/1999/04/crypto/)_. And the National Security Agency (NSA) claims to have knowledge of this type of algorithm as early as 1966. For some additional insight on who knew what when, see Steve Bellovin's "[The Prehistory of Public Key Cryptography](https://www.cs.columbia.edu/~smb/nsam-160/)."

___

#### Hash Functions

Hash functions, also called _message digests_ and _one-way encryption_, are algorithms that, in essence, use no key (Figure 1C). Instead, a fixed-length hash value is computed based upon the plaintext that makes it impossible for either the contents or length of the plaintext to be recovered. Hash algorithms are typically used to provide a _digital fingerprint_ of a file's contents, often used to ensure that the file has not been altered by an intruder or virus. Hash functions are also commonly employed by many operating systems to encrypt passwords. Hash functions, then, provide a mechanism to ensure the integrity of a file.

Hash functions are also designed so that small changes in the input produce significant differences in the hash value, for example:

> Hash string 1: The quick brown fox jumps over the lazy dog  
> Hash string 2: The quick brown fox jumps over the lazy dog.
> 
> MD5 \[hash string 1\] = 37c4b87edffc5d198ff5a185cee7ee09  
> MD5 \[hash string 2\] = 0d7006cd055e94cf614587e1d2ae0c8e
> 
> SHA1 \[hash string 1\] = be417768b5c3c5c1d9bcb2e7c119196dd76b5570  
> SHA1 \[hash string 2\] = 9c04cd6372077e9b11f70ca111c9807dc7137e4b
> 
> RIPEMD160 \[hash string 1\] = ee061f0400729d0095695da9e2c95168326610ff  
> RIPEMD160 \[hash string 2\] = 99b90925a0116c302984211dbe25b5343be9059e

___

> Let me reiterate that hashes are **one-way** encryption. You cannot take a hash and "decrypt" it to find the original string that created it, despite the many web sites that claim or suggest otherwise, such as [CrackStation](https://crackstation.net/), [Hashes.com](https://hashes.com/en/decrypt/hash), [MD5 Online](http://www.md5online.org/), [**md5this**cracker](http://www.md5this.com/), [OnlineHashCrack](http://www.onlinehashcrack.com/), and [RainbowCrack](http://project-rainbowcrack.com/).
> 
> Note that these sites search databases and/or use [rainbow tables](https://en.wikipedia.org/wiki/Rainbow_table) to find a suitable string that produces the hash in question but one can't definitively guarantee what string originally produced the hash. This is an important distinction. Suppose that you want to crack someone's password, where the hash of the password is stored on the server. Indeed, all you then need is a string that produces the correct hash and you're in! However, you cannot prove that you have discovered the user's password, only a "duplicate key."

___

Hash algorithms in common use today include:

-   _Message Digest (MD) algorithms:_ A series of byte-oriented algorithms that produce a 128-bit hash value from an arbitrary-length message.
    
    -   _MD2 ([RFC 1319](https://www.rfc-editor.org/info/rfc1319)):_ Designed for systems with limited memory, such as smart cards. (MD2 has been relegated to historical status, per [RFC 6149](https://www.rfc-editor.org/info/rfc6149).)
        
    -   _MD4 ([RFC 1320](https://www.rfc-editor.org/info/rfc1320)):_ Developed by Rivest, similar to MD2 but designed specifically for fast processing in software. (MD4 has been relegated to historical status, per [RFC 6150](https://www.rfc-editor.org/info/rfc6150).)
        
    -   _MD5 ([RFC 1321](https://www.rfc-editor.org/info/rfc1321)):_ Also developed by Rivest after potential weaknesses were reported in MD4; this scheme is similar to MD4 but is slower because more manipulation is made to the original data. MD5 has been implemented in a large number of products although several weaknesses in the algorithm were demonstrated by German cryptographer Hans Dobbertin in 1996 (["Cryptanalysis of MD5 Compress"](http://cseweb.ucsd.edu/~bsy/dobbertin.ps)). (Updated security considerations for MD5 can be found in [RFC 6151](https://www.rfc-editor.org/info/rfc6151).)
    
-   _Secure Hash Algorithm (SHA):_ Algorithm for NIST's Secure Hash Standard (SHS), described in [FIPS PUB 180-4](http://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf) The status of NIST hash algorithms can be found on their "[Policy on Hash Functions](http://csrc.nist.gov/groups/ST/hash/policy.html)" page.
    
    -   SHA-1 produces a 160-bit hash value and was originally published as FIPS PUB 180-1 and [RFC 3174](https://www.rfc-editor.org/info/rfc3174). SHA-1 was deprecated by NIST as of the end of 2013 although it is still widely used.
        
    -   SHA-2, originally described in FIPS PUB 180-2 and eventually replaced by FIPS PUB 180-3 (and [FIPS PUB 180-4](http://csrc.nist.gov/publications/fips/fips180-4/fips-180-4.pdf)), comprises five algorithms in the SHS: SHA-1 plus SHA-224, SHA-256, SHA-384, and SHA-512 which can produce hash values that are 224, 256, 384, or 512 bits in length, respectively. SHA-2 recommends use of SHA-1, SHA-224, and SHA-256 for messages less than 2<sup>64</sup> bits in length, and employs a 512 bit block size; SHA-384 and SHA-512 are recommended for messages less than 2<sup>128</sup> bits in length, and employs a 1,024 bit block size. FIPS PUB 180-4 also introduces the concept of a truncated hash in SHA-512/_t_, a generic name referring to a hash value based upon the SHA-512 algorithm that has been truncated to _t_ bits; SHA-512/224 and SHA-512/256 are specifically described. SHA-224, -256, -384, and -512 are also described in [RFC 4634](https://www.rfc-editor.org/info/rfc4634).
        
    -   SHA-3 is the current SHS algorithm. Although there had not been any successful attacks on SHA-2, NIST decided that having an alternative to SHA-2 using a different algorithm would be prudent. In 2007, they launched a [SHA-3 Competition](http://csrc.nist.gov/groups/ST/hash/sha-3/) to find that alternative; a list of submissions can be found at [The SHA-3 Zoo](http://ehash.iaik.tugraz.at/wiki/The_SHA-3_Zoo). In 2012, NIST announced that after reviewing 64 submissions, the winner was [Keccak](http://keccak.noekeon.org/) (pronounced "catch-ack"), a family of hash algorithms based on [sponge functions](https://en.wikipedia.org/wiki/Sponge_function). The NIST version can support hash output sizes of 256 and 512 bits.
    
-   _[RIPEMD](http://www.esat.kuleuven.ac.be/~bosselae/ripemd160.html):_ A series of message digests that initially came from the RIPE (RACE Integrity Primitives Evaluation) project. [RIPEMD-160](http://www.esat.kuleuven.be/~bosselae/ripemd160/pdf/AB-9601/AB-9601.pdf) was designed by Hans Dobbertin, Antoon Bosselaers, and Bart Preneel, and optimized for 32-bit processors to replace the then-current 128-bit hash functions. Other versions include RIPEMD-256, RIPEMD-320, and RIPEMD-128.
    
-   _[eD2k](https://en.wikipedia.org/wiki/Ed2k_URI_scheme#eD2k_hash_algorithm):_ Named for the EDonkey2000 Network (eD2K), the [eD2k](https://en.wikipedia.org/wiki/EDonkey_network#Hash_identification) hash is a _root hash_ of an MD4 hash list of a given file. A root hash is used on peer-to-peer file transfer networks, where a file is broken into chunks; each chunk has its own MD4 hash associated with it and the server maintains a file that contains the hash list of all of the chunks. The root hash is the hash of the hash list file.
    
-   _[HAVAL (HAsh of VAriable Length)](http://ro.uow.edu.au/cgi/viewcontent.cgi?article=2096&context=infopapers):_ Designed by Y. Zheng, J. Pieprzyk and J. Seberry, a hash algorithm with many levels of security. HAVAL can create hash values that are 128, 160, 192, 224, or 256 bits in length. More details can be found in ["HAVAL - A one-way hashing algorithm with variable length output"](https://works.bepress.com/jseberry/192/) by Zheng, Pieprzyk, and Seberry (AUSCRYPT '92).
    
-   _[The Skein Hash Function Family](https://www.schneier.com/academic/skein/):_ [The Skein Hash Function Family](https://en.wikipedia.org/wiki/Skein_(hash_function)) was proposed to NIST in their 2010 hash function competition. Skein is fast due to using just a few simple computational primitives, secure, and very flexible — per the [specification](http://www.skein-hash.info/sites/default/files/skein1.3.pdf), it can be used as a straight-forward hash, MAC, HMAC, digital signature hash, key derivation mechanism, stream cipher, or pseudo-random number generator. Skein supports internal state sizes of 256, 512 and 1024 bits, and arbitrary output lengths.
    
-   _[SM3](https://tools.ietf.org/id/draft-oscca-cfrg-sm3-02.html):_ SM3 is a 256-bit hash function operating on 512-bit input blocks. Part of a Chinese National Standard, SM3 is issued by the Chinese State Cryptographic Authority as _GM/T 0004-2012: SM3 cryptographic hash algorithm_ (2012) and _GB/T 32905-2016: Information security techniquesâ€”SM3 cryptographic hash algorithm_ (2016). More information can also be found at the [SM3 (hash function)](https://en.wikipedia.org/wiki/SM3_(hash_function)) page.
    
-   _[Tiger](http://www.cs.technion.ac.il/~biham/Reports/Tiger/):_ Designed by Ross Anderson and Eli Biham, Tiger is designed to be secure, run efficiently on 64-bit processors, and easily replace MD4, MD5, SHA and SHA-1 in other applications. Tiger/192 produces a 192-bit output and is compatible with 64-bit architectures; Tiger/128 and Tiger/160 produce a hash of length 128 and 160 bits, respectively, to provide compatibility with the other hash functions mentioned above.
    
-   _[Whirlpool](http://www.seas.gwu.edu/~poorvi/Classes/CS381_2007/Whirlpool.pdf):_ Designed by V. Rijmen (co-inventor of Rijndael) and P.S.L.M. Barreto, Whirlpool is one of two hash functions endorsed by the [NESSIE](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#nessie) competition (the other being SHA). Whirlpool operates on messages less than 2<sup>256</sup> bits in length and produces a message digest of 512 bits. The design of this hash function is very different than that of MD5 and SHA-1, making it immune to the types of attacks that succeeded on those hashes.
    

Readers might be interested in [HashCalc](http://www.slavasoft.com/hashcalc/), a Windows-based program that calculates hash values using a dozen algorithms, including MD5, SHA-1 and several variants, RIPEMD-160, and Tiger. Command line utilities that calculate hash values include [sha\_verify](http://www.dmares.com/maresware/html/sha_verify.htm) by Dan Mares (Windows; supports MD5, SHA-1, SHA-2) and [md5deep](http://md5deep.sourceforge.net/) (cross-platform; supports MD5, SHA-1, SHA-256, Tiger, and Whirlpool).

___

> **A digression on hash collisions.** Hash functions are sometimes misunderstood and some sources claim that no two files can have the same hash value. This is in theory, if not in fact, incorrect. Consider a hash function that provides a 128-bit hash value. There are, then, 2<sup>128</sup> possible hash values. But there are an infinite number of possible files and ∞ >> 2<sup>128</sup>. Therefore, there have to be multiple files — in fact, there have to be an infinite number of files! — that have the same 128-bit hash value. (Now, while even this is theoretically correct, it is not true in practice because hash algorithms are designed to work with a limited message size, as mentioned above. For example, SHA-1, SHA-224, and SHA-256 produce hash values that are 160, 224, and 256 bits in length, respectively, and limit the message length to less than 2<sup>64</sup> bits; SHA-384 and all SHA-256 variants limit the message length to less than 2<sup>128</sup> bits. Nevertheless, hopefully you get my point — and, alas, even if you don't, do know that there are multiple files that have the same MD5 or SHA-1 hash values.)
> 
> The difficulty is not necessarily in finding two files with the same hash, but in finding a second file that has the same hash value as a given first file. Consider this example. A human head has, generally, no more than ~150,000 hairs. Since there are more than 7 billion people on earth, we know that there are a lot of people with the same number of hairs on their head. Finding two people with the same number of hairs, then, would be relatively simple. The harder problem is choosing one person (say, you, the reader) and then finding another person who has the same number of hairs on their head as you have on yours.
> 
> This is somewhat similar to the [Birthday Problem](https://en.wikipedia.org/wiki/Birthday_problem). We know from probability that if you choose a random group of ~23 people, the probability is about 50% that two will share a birthday (the probability goes up to 99.9% with a group of 70 people). However, if you randomly select one person in a group of 23 and try to find a match to that person, the probability is only about 6% of finding a match; you'd need a group of 253 for a 50% probability of a shared birthday to one of the people chosen at random (and a group of more than 4,000 to obtain a 99.9% probability).
> 
> What is hard to do, then, is to try to create a file that matches a given hash value so as to force a hash value collision — which is the reason that hash functions are used extensively for information security and computer forensics applications. Alas, researchers as far back as 2004 found that _practical_ collision attacks could be launched on MD5, SHA-1, and other hash algorithms and, today, it is generally recognized that MD5 and SHA-1 are pretty much broken. Readers interested in this problem should read the following:
> 
> -   AccessData. (2006, April). [_MD5 Collisions: The Effect on Computer Forensics_](https://ad-pdf.s3.amazonaws.com/papers/wp.MD5_Collisions.en_us.pdf). AccessData White Paper.
> -   Burr, W. (2006, March/April). [Cryptographic hash standards: Where do we go from here?](https://ieeexplore.ieee.org/document/1621069) _IEEE Security & Privacy_, _4_(2), 88-91.
> -   Dwyer, D. (2009, June 3). [SHA-1 Collision Attacks Now 2<sup>52</sup>](http://www.secureworks.com/resources/blog/research-20935/). _SecureWorks Research blog_.
> -   Gutman, P., Naccache, D., & Palmer, C.C. (2005, May/June). [When hashes collide](https://researchspace.auckland.ac.nz/bitstream/handle/2292/269/269.pdf). _IEEE Security & Privacy_, _3_(3), 68-71.
> -   Kessler, G.C. (2016). [The Impact of MD5 File Hash Collisions on Digital Forensic Imaging](https://www.garykessler.net/library/Kessler_Hash_Collisions_MD5.pdf). _Journal of Digital Forensics, Security & Law_, _11_(4), 129-138.
> -   Kessler, G.C. (2016). [The Impact of SHA-1 File Hash Collisions on Digital Forensic Imaging: A Follow-Up Experiment](https://www.garykessler.net/library/Kessler_Hash_Collisions_SHA1.pdf). _Journal of Digital Forensics, Security & Law_, _11_(4), 139-148.
> -   Klima, V. (2005, March). [Finding MD5 Collisions - a Toy For a Notebook.](http://cryptography.hyperlink.cz/md5/MD5_collisions.pdf)
> -   Lee, R. (2009, January 7). [Law Is Not A Science: Admissibility of Computer Evidence and MD5 Hashes](https://www.sans.org/blog/law-is-not-a-science-admissibility-of-computer-evidence-and-md5-hashes/). _SANS Computer Forensics blog._
> -   Leurent, G. & Peyrin, T. (2020, January). [SHA-1 is a Shambles: First Chosen-Prefix Collision on SHA-1 and Application to the PGP Web of Trust](https://sha-mbles.github.io/Shambles_RWC.pdf). _Real World Crypto 2020_.
> -   Leurent, G. & Peyrin, T. (2020, January). [SHA-1 is a Shambles: First Chosen-Prefix Collision on SHA-1 and Application to the PGP Web of Trust](https://eprint.iacr.org/2020/014.pdf). (paper)
> -   Stevens, M., Bursztein, E., Karpman, P., Albertini, A., & Markov, Y. (2017). [The first collision for full SHA-1](https://shattered.it/static/shattered.pdf).
> -   Stevens, M., Karpman, P., & Peyrin, T. (2015, October 8). [Freestart collision on full SHA-1](https://eprint.iacr.org/2015/967.pdf). Cryptology ePrint Archive, Report 2015/967.
> -   Thompson, E. (2005, February). MD5 collisions and the impact on computer forensics. _Digital Investigation_, _2_(1), 36-40.
> -   Wang, X., Feng, D., Lai, X., & Yu, H. (2004, August). [Collisions for Hash Functions MD4, MD5, HAVAL-128 and RIPEMD.](http://eprint.iacr.org/2004/199.pdf)
> -   Wang, X., Yin, Y.L., & Yu, H. (2005, February 13). [Collision Search Attacks on SHA1.](http://www.c4i.org/erehwon/shanote.pdf)
> 
> Readers are also referred to the [Eindhoven University of Technology HashClash Project](http://www.win.tue.nl/hashclash/rogue-ca/) Web site. for For additional information on hash functions, see David Hopwood's [MessageDigest Algorithms](http://www.users.zetnet.co.uk/hopwood/crypto/scan/md.html) page and Peter Selinger's [MD5 Collision Demo](http://crppit.epfl.ch/documentation/Hash_Function/Examples/Peter%20Selinger%20%20MD5%20Collision%20Demo.htm) page. For historical purposes, take a look at the situation with hash collisions, circa 2005, in [RFC 4270](https://www.rfc-editor.org/info/rfc4270).
> 
> In October 2015, the SHA-1 Freestart Collision was announced; see a report by [Bruce Schneier](https://www.schneier.com/blog/archives/2015/10/sha-1_freestart.html) and [the developers](https://sites.google.com/site/itstheshappening/) of the attack (as well as the paper above by Stevens et al. (2015)). In February 2017, the first SHA-1 collision was announced on the [Google Security Blog](https://security.googleblog.com/2017/02/announcing-first-sha1-collision.html) and Centrum Wiskunde & Informatica's [Shattered](https://shattered.it/) page. See also the paper by Stevens et al. (2017), listed above. If this isn't enough, see the [SHA-1 is a Shambles](https://sha-mbles.github.io/) Web page and the Leurent & Peyrin paper, listed above.
> 
> For an interesting twist on this discussion, read about the _Nostradamus_ attack reported at [Predicting the winner of the 2008 US Presidential Elections using a Sony PlayStation 3](http://www.win.tue.nl/hashclash/Nostradamus/) (by M. Stevens, A.K. Lenstra, and B. de Weger, November 2007).

___

Finally, note that certain extensions of hash functions are used for a variety of information security and digital forensics applications, such as:

-   _Hash libraries_, aka _hashsets_, are sets of hash values corresponding to known files. A hashset containing the hash values of all files known to be a part of a given operating system, for example, could form a set of _known good files_, and could be ignored in an investigation for malware or other suspicious file, whereas as hash library of known child pornographic images could form a set of _known bad files_ and be the target of such an investigation.
-   _Rolling hashes_ refer to a set of hash values that are computed based upon a fixed-length "sliding window" through the input. As an example, a hash value might be computed on bytes 1-10 of a file, then on bytes 2-11, 3-12, 4-13, etc.
-   _Fuzzy hashes_ are an area of intense research and represent hash values that represent two inputs that are similar. Fuzzy hashes are used to detect documents, images, or other files that are close to each other with respect to content. See "[Fuzzy Hashing](http://old.dfrws.org/2006/proceedings/12-Kornblum.pdf)" by Jesse Kornblum for a good treatment of this topic.

#### Why Three Encryption Techniques?

So, why are there so many different types of cryptographic schemes? Why can't we do everything we need with just one?

The answer is that each scheme is optimized for some specific cryptographic application(s). Hash functions, for example, are well-suited for ensuring data integrity because any change made to the contents of a message will result in the receiver calculating a different hash value than the one placed in the transmission by the sender. Since it is highly unlikely that two different messages will yield the same hash value, data integrity is ensured to a high degree of confidence.

Secret key cryptography, on the other hand, is ideally suited to encrypting messages, thus providing privacy and confidentiality. The sender can generate a _session key_ on a per-message basis to encrypt the message; the receiver, of course, needs the same session key in order to decrypt the message.

Key exchange, of course, is a key application of public key cryptography (no pun intended). Asymmetric schemes can also be used for non-repudiation and user authentication; if the receiver can obtain the session key encrypted with the sender's private key, then only this sender could have sent the message. Public key cryptography could, theoretically, also be used to encrypt messages although this is rarely done because secret key cryptography algorithms can generally be executed up to 1000 times faster than public key cryptography algorithms.

![](https://www.garykessler.net/library/images/crypto_3ways.gif)

**FIGURE 4: Use of the three cryptographic techniques for secure communication.**

Figure 4 puts all of this together and shows how a _hybrid cryptographic_ scheme combines all of these functions to form a secure transmission comprising a _digital signature_ and _digital envelope_. In this example, the sender of the message is Alice and the receiver is Bob.

A digital envelope comprises an encrypted message and an encrypted session key. Alice uses secret key cryptography to encrypt her message using the _session key_, which she generates at random with each session. Alice then encrypts the session key using Bob's public key. The encrypted message and encrypted session key together form the digital envelope. Upon receipt, Bob recovers the session secret key using his private key and then decrypts the encrypted message.

The digital signature is formed in two steps. First, Alice computes the hash value of her message; next, she encrypts the hash value with her private key. Upon receipt of the digital signature, Bob recovers the hash value calculated by Alice by decrypting the digital signature with Alice's public key. Bob can then apply the hash function to Alice's original message, which he has already decrypted (see previous paragraph). If the resultant hash value is not the same as the value supplied by Alice, then Bob knows that the message has been altered; if the hash values are the same, Bob should believe that the message he received is identical to the one that Alice sent.

This scheme also provides nonrepudiation since it proves that Alice sent the message; if the hash value recovered by Bob using Alice's public key proves that the message has not been altered, then only Alice could have created the digital signature. Bob also has proof that he is the intended receiver; if he can correctly decrypt the message, then he must have correctly decrypted the session key meaning that his is the correct private key.

This diagram purposely suggests a cryptosystem where the session key is used for just a single session. Even if this session key is somehow broken, only this session will be compromised; the session key for the next session is not based upon the key for this session, just as this session's key was not dependent on the key from the previous session. This is known as [Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy); you might lose one session key due to a compromise but you won't lose all of them. (This was an issue in the 2014 OpenSSL vulnerability known as [Heartbleed](http://heartbleed.com/).)

The system described here is one where we basically encrypt the secret session key with the receiver's public key. While this generic scheme works well, it causes some incompatibilities in practice. [RFC 9180](https://www.rfc-editor.org/info/rfc9180), released in early 2022, describes a new approach to building a Hybrid Public Key Encryption (HPKE) process. HPKE was designed specifically to be simple, reusable, and future-proof. A nice description of the process can be found in a blog posting titled, "[HPKE: Standardizing Public-Key Encryption (Finally!)](https://blog.cloudflare.com/hybrid-public-key-encryption/)" (C. Wood).

#### The Significance of Key Length

In a 1998 article in the industry literature, a writer made the claim that 56-bit keys did not provide as adequate protection for DES at that time as they did in 1975 because computers were 1000 times faster in 1998 than in 1975. Therefore, the writer went on, we needed 56,000-bit keys in 1998 instead of 56-bit keys to provide adequate protection. The conclusion was then drawn that because 56,000-bit keys are infeasible (_true_), we should accept the fact that we have to live with weak cryptography (_false!_). The major error here is that the writer did not take into account that the number of possible key values double whenever a single bit is added to the key length; thus, a 57-bit key has twice as many values as a 56-bit key (because 2<sup>57</sup> is two times 2<sup>56</sup>). In fact, a 66-bit key would have 1024 times more values than a 56-bit key.

But this does bring up the question — "What is the significance of key length as it affects the level of protection?"

In cryptography, size does matter. The larger the key, the harder it is to crack a block of encrypted data. The reason that large keys offer more protection is almost obvious; computers have made it easier to attack ciphertext by using brute force methods rather than by attacking the mathematics (which are generally well-known anyway). With a brute force attack, the attacker merely generates every possible key and applies it to the ciphertext. Any resulting plaintext that makes sense offers a candidate for a legitimate key. This was the basis, of course, of the EFF's attack on DES.

Until the mid-1990s or so, brute force attacks were beyond the capabilities of computers that were within the budget of the attacker community. By that time, however, significant compute power was typically available and accessible. General-purpose computers such as PCs were already being used for brute force attacks. For serious attackers with money to spend, such as some large companies or governments, Field Programmable Gate Array (FPGA) or Application-Specific Integrated Circuits (ASIC) technology offered the ability to build specialized chips that could provide even faster and cheaper solutions than a PC. As an example, the AT&T Optimized Reconfigurable Cell Array (ORCA) FPGA chip cost about $200 and could test 30 million DES keys per second, while a $10 ASIC chip could test 200 million DES keys per second; compare that to a PC which might be able to test 40,000 keys per second. Distributed attacks, harnessing the power of up to tens of thousands of powerful CPUs, are now commonly employed to try to brute-force crypto keys.

<table><caption><b>TABLE 2. Minimum Key Lengths for Symmetric Ciphers.</b></caption><tbody><tr><th rowspan="2">Type of Attacker</th><th rowspan="2">Budget</th><th rowspan="2">Tool</th><th colspan="2">Time and Cost<br>Per Key Recovered</th><th rowspan="2">Key Length Needed<br>For Protection<br>In Late-1995</th></tr><tr><th>40 bits</th><th>56 bits</th></tr><tr><td rowspan="2">Pedestrian Hacker</td><td>Tiny</td><td>Scavenged<br>computer<br>time</td><td>1 week</td><td>Infeasible</td><td>45</td></tr><tr><td>$400</td><td>FPGA</td><td>5 hours<br>($0.08)</td><td>38 years<br>($5,000)</td><td>50</td></tr><tr><td>Small Business</td><td>$10,000</td><td>FPGA</td><td>12 minutes<br>($0.08)</td><td>18 months<br>($5,000)</td><td>55</td></tr><tr><td rowspan="2">Corporate Department</td><td rowspan="2">$300K</td><td>FPGA</td><td>24 seconds<br>($0.08)</td><td>19 days<br>($5,000)</td><td rowspan="2">60</td></tr><tr><td>ASIC</td><td>0.18 seconds<br>($0.001)</td><td>3 hours<br>($38)</td></tr><tr><td rowspan="2">Big Company</td><td rowspan="2">$10M</td><td>FPGA</td><td>7 seconds<br>($0.08)</td><td>13 hours<br>($5,000)</td><td rowspan="2">70</td></tr><tr><td>ASIC</td><td>0.005 seconds<br>($0.001)</td><td>6 minutes<br>($38)</td></tr><tr><td>Intelligence Agency</td><td>$300M</td><td>ASIC</td><td>0.0002 seconds<br>($0.001)</td><td>12 seconds<br>($38)</td><td>75</td></tr></tbody></table>

Table 2 — from a 1996 article discussing both why exporting 40-bit keys was, in essence, no crypto at all _and_ why DES' days were numbered — shows what DES key sizes were needed to protect data from attackers with different time and financial resources. This information was not merely academic; one of the basic tenets of any security system is to have an idea of _what_ you are protecting and _from whom_ are you protecting it! The table clearly shows that a 40-bit key was essentially worthless against even the most unsophisticated attacker. On the other hand, 56-bit keys were fairly strong unless you might be subject to some pretty serious corporate or government espionage. But note that even 56-bit keys were clearly on the decline in their value and that the times in the table were worst cases.

So, how big is big enough? DES, invented in 1975, was still in use at the turn of the century, nearly 25 years later. If we take that to be a design criteria (i.e., a 20-plus year lifetime) and we believe Moore's Law ("computing power doubles every 18 months"), then a key size extension of 14 bits (i.e., a factor of more than 16,000) should be adequate. The 1975 DES proposal suggested 56-bit keys; by 1995, a 70-bit key would have been required to offer equal protection and an 85-bit key necessary by 2015.

A 256- or 512-bit SKC key will probably suffice for some time because that length keeps us ahead of the brute force capabilities of the attackers. Note that while a large key is good, a huge key may not always be better; for example, expanding PKC keys beyond the current 2048- or 4096-bit lengths doesn't add any necessary protection at this time. Weaknesses in cryptosystems are largely based upon key management rather than weak keys.

Much of the discussion above, including the table, is based on the paper ["Minimal Key Lengths for Symmetric Ciphers to Provide Adequate Commercial Security"](http://www.schneier.com/paper-keylength.html) by M. Blaze, W. Diffie, R.L. Rivest, B. Schneier, T. Shimomura, E. Thompson, and M. Wiener (1996).

The most effective large-number factoring methods today use a mathematical Number Field Sieve to find a certain number of relationships and then uses a matrix operation to solve a linear equation to produce the two prime factors. The sieve step actually involves a large number of operations that can be performed in parallel; solving the linear equation, however, requires a supercomputer. Indeed, finding the solution to the RSA-140 challenge in February 1999 — factoring a 140-digit (465-bit) prime number — required 200 computers across the Internet about 4 weeks for the first step and a Cray computer 100 hours and 810 MB of memory to do the second step.

In early 1999, Shamir (of RSA fame) described a new machine that could increase factorization speed by 2-3 orders of magnitude. Although no detailed plans were provided nor is one known to have been built, the concepts of [TWINKLE (The Weizmann Institute Key Locating Engine)](https://en.wikipedia.org/wiki/TWINKLE) could result in a specialized piece of hardware that would cost about $5000 and have the processing power of 100-1000 PCs. There still appear to be many engineering details that have to be worked out before such a machine could be built. Furthermore, the hardware improves the sieve step only; the matrix operation is not optimized at all by this design and the complexity of this step grows rapidly with key length, both in terms of processing time and memory requirements. Nevertheless, this plan conceptually puts 512-bit keys within reach of being factored. Although most PKC schemes allow keys that are 1024 bits and longer, Shamir claims that 512-bit RSA keys "protect 95% of today's E-commerce on the Internet." (See Bruce Schneier's [Crypto-Gram (May 15, 1999)](https://www.schneier.com/crypto-gram/archives/1999/0515.html#twinkle) for more information.)

It is also interesting to note that while cryptography is good and strong cryptography is better, long keys may disrupt the nature of the randomness of data files. Shamir and van Someren (["Playing hide and seek with stored keys"](http://hawaii.ms11.net/keyhide2.pdf)) have noted that a new generation of viruses can be written that will find files encrypted with long keys, making them easier to find by intruders and, therefore, more prone to attack.

Finally, U.S. government policy has tightly controlled the export of crypto products since World War II. Until the mid-1990s, export outside of North America of cryptographic products using keys greater than 40 bits in length was prohibited, which made those products essentially worthless in the marketplace, particularly for electronic commerce; today, crypto products are widely available on the Internet without restriction. The U.S. Department of Commerce Bureau of Industry and Security maintains an [Encryption FAQ](https://www.opensource.im/encryption/encryption-faqs-bureau-of-industry-and-security.php) web page with more information about the current state of encryption registration.

___

> Without meaning to editorialize too much in this tutorial, a bit of historical context might be helpful. In the mid-1990s, the U.S. Department of Commerce still classified cryptography as a _munition_ and limited the export of any products that contained crypto. For that reason, browsers in the 1995 era, such as Internet Explorer and Netscape, had a domestic version with 128-bit encryption (downloadable only in the U.S.) and an export version with 40-bit encryption. Many cryptographers felt that the export limitations should be lifted because they only applied to U.S. products and seemed to have been put into place by policy makers who believed that only the U.S. knew how to build strong crypto algorithms, ignoring the work ongoing in Australia, Canada, Israel, South Africa, the U.K., and other locations in the 1990s. Those restrictions were lifted by 1996 or 1997, but there is still a prevailing attitude, apparently, that U.S. crypto algorithms are the only strong ones around; consider Bruce Schneier's blog in June 2016 titled "[CIA Director John Brennan Pretends Foreign Cryptography Doesn't Exist](https://www.schneier.com/blog/archives/2016/06/cia_director_jo.html)." Cryptography is a decidedly international game today; note the many countries mentioned above as having developed various algorithms, not the least of which is the fact that NIST's Advanced Encryption Standard employs an algorithm submitted by cryptographers from Belgium. For more evidence, see Schneier's [Worldwide Encryption Products Survey](https://www.schneier.com/blog/archives/2016/02/worldwide_encry.html) (February 2016).

___

On a related topic, public key crypto schemes can be used for several purposes, including key exchange, digital signatures, authentication, and more. In those PKC systems used for SKC key exchange, the PKC key lengths are chosen so as to be resistant to some selected level of attack. The length of the secret keys exchanged via that system have to have at least the same level of attack resistance. Thus, the three parameters of such a system — system strength, secret key strength, and public key strength — must be matched. This topic is explored in more detail in _Determining Strengths For Public Keys Used For Exchanging Symmetric Keys_ ([RFC 3766](https://www.rfc-editor.org/info/rfc3766)).

### TRUST MODELS

Secure use of cryptography requires trust. While secret key cryptography can ensure message confidentiality and hash codes can ensure integrity, none of this works without trust. In SKC, Alice and Bob had to share a secret key. PKC solved the secret distribution problem, but how does Alice really know that Bob is who he says he is? Just because Bob has a public and private key, and purports to be "Bob," how does Alice know that a malicious person (Mallory) is not pretending to be Bob?

There are a number of _trust models_ employed by various cryptographic schemes. This section will explore three of them:

-   The web of trust employed by Pretty Good Privacy (PGP) users, who hold their own set of trusted public keys.
-   Kerberos, a secret key distribution scheme using a trusted third party.
-   Certificates, which allow a set of trusted third parties to authenticate each other and, by implication, each other's users.

Each of these trust models differs in complexity, general applicability, scope, and scalability.

#### PGP Web of Trust

Pretty Good Privacy (described more below in [Section 5.5](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#pgp)) is a widely used private e-mail scheme based on public key methods. A PGP user maintains a local keyring of all their known and trusted public keys. The user makes their own determination about the trustworthiness of a key using what is called a "web of trust."

![](https://www.garykessler.net/library/images/crypto_gpg.png)

**FIGURE 5: GPG keychain.**

Figure 5 shows a PGP-formatted keychain from the GNU Privacy Guard (GPG) software, an implementation of the OpenPGP standard. This is a section of my keychain, so only includes public keys from individuals whom I know and, presumably, trust. Note that keys are associated with e-mail addresses rather than individual names.

In general, the PGP Web of trust works as follows. Suppose that Alice needs Bob's public key. Alice could just ask Bob for it directly via e-mail or download the public key from a PGP key server; this server might a well-known PGP key repository or a site that Bob maintains himself. In fact, Bob's public key might be stored or listed in many places. (My public key, for example, can be found at [_https://www.garykessler.net/pubkey.html_](https://www.garykessler.net/pubkey.html) or at several public PGP key servers, including [_https://keys.openpgp.org_](https://keys.openpgp.org/vks/v1/by-fingerprint/83588BC20C06439AD1C311BB3762EEE0D9E84EC1).) Alice is prepared to believe that Bob's public key, as stored at these locations, is valid.

Suppose Carol claims to hold Bob's public key and offers to give the key to Alice. How does Alice know that Carol's version of Bob's key is valid or if Carol is actually giving Alice a key that will allow Mallory access to messages? The answer is, "It depends." If Alice trusts Carol and Carol says that she thinks that her version of Bob's key is valid, then Alice _may_ — at _her_ option — trust that key. And trust is not necessarily transitive; if Dave has a copy of Bob's key and Carol trusts Dave, it does not necessarily follow that Alice trusts Dave even if she does trust Carol.

The point here is that who Alice trusts and how she makes that determination is strictly up to Alice. PGP makes no statement and has no protocol about how one user determines whether they trust another user or not. In any case, encryption and signatures based on public keys can only be used when the appropriate public key is on the user's keyring.

#### Kerberos

[Kerberos](http://web.mit.edu/kerberos/) is a commonly used authentication scheme on the Internet. Developed by MIT's Project Athena, Kerberos is named for the three-headed dog who, according to Greek mythology, guards the entrance of Hades (rather than the exit, for some reason!).

Kerberos employs a client/server architecture and provides user-to-server authentication rather than host-to-host authentication. In this model, security and authentication will be based on secret key technology where every host on the network has its own secret key. It would clearly be unmanageable if every host had to know the keys of all other hosts so a secure, trusted host somewhere on the network, known as a Key Distribution Center (KDC), knows the keys for all of the hosts (or at least some of the hosts within a portion of the network, called a _realm_). In this way, when a new node is brought online, only the KDC and the new node need to be configured with the node's key; keys can be distributed physically or by some other secure means.

![](https://www.garykessler.net/library/images/crypto_kerberos.gif)

**FIGURE 6: Kerberos architecture.**


The Kerberos Server/KDC has two main functions (Figure 6), known as the Authentication Server (AS) and Ticket-Granting Server (TGS). The steps in establishing an authenticated session between an application client and the application server are:

1.  The Kerberos client software establishes a connection with the Kerberos server's AS function. The AS first authenticates that the client is who it purports to be. The AS then provides the client with a secret key for this login session (the _TGS session key_) and a ticket-granting ticket (TGT), which gives the client permission to talk to the TGS. The ticket has a finite lifetime so that the authentication process is repeated periodically.
2.  The client now communicates with the TGS to obtain the Application Server's key so that it (the client) can establish a connection to the service it wants. The client supplies the TGS with the TGS session key and TGT; the TGS responds with an application session key (ASK) and an encrypted form of the Application Server's secret key; this secret key is _never_ sent on the network in any other form.
3.  The client has now authenticated itself _and_ can prove its identity to the Application Server by supplying the Kerberos ticket, application session key, and encrypted Application Server secret key. The Application Server responds with similarly encrypted information to authenticate itself to the client. At this point, the client can initiate the intended service requests (e.g., Telnet, FTP, HTTP, or e-commerce transaction session establishment).

The current version of this protocol is Kerberos V5 (described in [RFC 1510](https://www.rfc-editor.org/info/rfc1510)). While the details of their operation, functional capabilities, and message formats are different, the conceptual overview above pretty much holds for both. One primary difference is that Kerberos V4 uses only DES to generate keys and encrypt messages, while V5 allows other schemes to be employed (although DES is still the most widely algorithm used).

#### Public Key Certificates and Certificate Authorities

_Certificates_ and _Certificate Authorities (CA)_ are necessary for widespread use of cryptography for e-commerce applications. While a combination of secret and public key cryptography can solve the business issues discussed above, crypto cannot alone address the trust issues that must exist between a customer and vendor in the very fluid, very dynamic e-commerce relationship. How, for example, does one site obtain another party's public key? How does a recipient determine if a public key really belongs to the sender? How does the recipient know that the sender is using their public key for a legitimate purpose for which they are authorized? When does a public key expire? How can a key be revoked in case of compromise or loss?

The basic concept of a certificate is one that is familiar to all of us. A driver's license, credit card, or SCUBA certification, for example, identify us to others, indicate something that we are authorized to do, have an expiration date, and identify the authority that granted the certificate.

As complicated as this may sound, it really isn't. Consider driver's licenses. I have one issued by the State of Florida. The license establishes my identity, indicates the type of vehicles that I can operate and the fact that I must wear corrective lenses while doing so, identifies the issuing authority, and notes that I am an organ donor. When I drive in other states, the other jurisdictions throughout the U.S. recognize the authority of Florida to issue this "certificate" and they trust the information it contains. When I leave the U.S., everything changes. When I am in Aruba, Australia, Canada, Israel, and many other countries, they will accept not the Florida license, per se, but _any_ license issued in the U.S. This analogy represents the certificate trust chain, where even certificates carry certificates.

For purposes of electronic transactions, certificates are digital documents. The specific functions of the certificate include:

-   _Establish identity:_ Associate, or _bind_, a public key to an individual, organization, corporate position, or other entity.
-   _Assign authority:_ Establish what actions the holder may or may not take based upon this certificate.
-   _Secure confidential information_ (e.g., encrypting the session's symmetric key for data confidentiality).

Typically, a certificate contains a public key, a name, an expiration date, the name of the authority that issued the certificate (and, therefore, is vouching for the identity of the user), a serial number, any pertinent policies describing how the certificate was issued and/or how the certificate may be used, the digital signature of the certificate issuer, and perhaps other information.

![](https://www.garykessler.net/library/images/crypto_cert.png)

**FIGURE 7: VeriSign Class 3 certificate.**

A sample abbreviated certificate is shown in Figure 7. This is a typical certificate found in a browser, in this case, Mozilla Firefox (MacOS). While this is a certificate issued by VeriSign, many root-level certificates can be found shipped with browsers. When the browser makes a connection to a secure Web site, the Web server sends its public key certificate to the browser. The browser then checks the certificate's signature against the public key that it has stored; if there is a match, the certificate is taken as valid and the Web site verified by this certificate is considered to be "trusted."

The most widely accepted certificate format is the one defined in International Telecommunication Union Telecommunication Standardization Sector (ITU-T) Recommendation X.509. Rec. X.509 is a specification used around the world and any applications complying with X.509 can share certificates. Most certificates today comply with X.509 Version 3 and contain the following information:

-   Version number
-   Certificate serial number
-   Signature algorithm identifier
-   Issuer's name and unique identifier
-   Validity (or operational) period
-   Subject's name and unique identifier
-   Subject public key information
-   Standard extensions

-   Certificate appropriate use definition
-   Key usage limitation definition
-   Certificate policy information

-   Other extensions

-   Application-specific
-   CA-specific

Certificate authorities are the repositories for public keys and can be any agency that issues certificates. A company, for example, may issue certificates to its employees, a college/university to its students, a store to its customers, an Internet service provider to its users, or a government to its constituents.

When a sender needs an intended receiver's public key, the sender must get that key from the receiver's CA. That scheme is straight-forward if the sender and receiver have certificates issued by the same CA. If not, how does the sender know to _trust_ the foreign CA? One industry wag has noted, about trust: "You are either born with it or have it granted upon you." Thus, some CAs will be trusted because they are known to be reputable, such as the CAs operated by AT&T Services, [Comodo](https://ssl.comodo.com/), [DigiCert](https://www.digicert.com/) (formerly GTE Cybertrust), [EnTrust](https://www.entrust.com/ssl-certificates/), [Broadcom](https://techdocs.broadcom.com/us/en/ca-enterprise-software/devops/devtest-solutions/10-4/administering/security/using-ssl-to-secure-communication/ssl-certificates.html) (formerly Symantec, formerly VeriSign), and [Thawte](https://www.thawte.com/ssl/). CAs, in turn, form trust relationships with other CAs. Thus, if a user queries a foreign CA for information, the user may ask to see a list of CAs that establish a "chain of trust" back to the user.

One major feature to look for in a CA is their identification policies and procedures. When a user generates a key pair and forwards the public key to a CA, the CA has to check the sender's identification and takes any steps necessary to assure itself that the request is really coming from the advertised sender. Different CAs have different identification policies and will, therefore, be trusted differently by other CAs. Verification of identity is just one of many issues that are part of a CA's Certification Practice Statement (CPS) and policies; other issues include how the CA protects the public keys in its care, how lost or compromised keys are revoked, and how the CA protects its own private keys.

As a final note, CAs are not immune to attack and certificates themselves are able to be counterfeited. One of the first such episodes occurred at the turn of the century; on January 29 and 30, 2001, two VeriSign Class 3 code-signing digital certificates were issued to an individual who fraudulently claimed to be a Microsoft employee ([CERT/CC CA-2001-04](https://www.cert.org/historical/advisories/CA-2001-04.cfm?) and [Microsoft Security Bulletin MS01-017 - Critical](https://technet.microsoft.com/en-us/library/security/ms01-017.aspx)). Problems have continued over the years; good write-ups on this can be found at "[Another Certification Authority Breached (the 12th!)](http://www.hackmageddon.com/2011/12/10/another-certification-authority-breached-the-12th/)" and "[How Cybercrime Exploits Digital Certificates](http://resources.infosecinstitute.com/cybercrime-exploits-digital-certificates/)." Readers are also urged to read "Certification Authorities Under Attack: A Plea for Certificate Legitimation" (Oppliger, R., January/February 2014, _IEEE Internet Computing_, _18_(1), 40-47).

As a partial way to address this issue, the Internet Security Research Group (ISRG) designed the [Automated Certificate Management Environment (ACME)](https://ietf-wg-acme.github.io/acme/) protocol. ACME is a communications protocol that streamlines the process of deploying a Public Key Infrastructure (PKI) by automating interactions between CAs and Web servers that wish to obtain a certificate. More information can be found at the [Let's Encrypt](https://letsencrypt.org/) Web site, an ACME-based CA service provided by the ISRG.

#### Summary

The paragraphs above describe three very different trust models. It is hard to say that any one is better than the others; it depends upon your application. One of the biggest and fastest growing applications of cryptography today, though, is electronic commerce (e-commerce), a term that itself begs for a formal definition.

PGP's web of trust is easy to maintain and very much based on the reality of users as people. The model, however, is limited; just how many public keys can a single user reliably store and maintain? And what if you are using the "wrong" computer when you want to send a message and can't access your keyring? How easy it is to revoke a key if it is compromised? PGP may also not scale well to an e-commerce scenario of secure communication between total strangers on short-notice.

Kerberos overcomes many of the problems of PGP's web of trust, in that it is scalable and its scope can be very large. However, it also requires that the Kerberos server have _a priori_ knowledge of all client systems prior to any transactions, which makes it unfeasible for "hit-and-run" client/server relationships as seen in e-commerce.

Certificates and the collection of CAs will form a PKI. In the early days of the Internet, every host had to maintain a list of every other host; the Domain Name System (DNS) introduced the idea of a distributed database for this purpose and the DNS is one of the key reasons that the Internet has grown as it has. A PKI will fill a similar void in the e-commerce and PKC realm.

While certificates and the benefits of a PKI are most often associated with electronic commerce, the applications for PKI are much broader and include secure electronic mail, payments and electronic checks, Electronic Data Interchange (EDI), secure transfer of Domain Name System (DNS) and routing information, electronic forms, and digitally signed documents. A single "global PKI" is still many years away, that is the ultimate goal of today's work as international electronic commerce changes the way in which we do business in a similar way in which the Internet has changed the way in which we communicate.

### CRYPTOGRAPHIC ALGORITHMS IN ACTION

The paragraphs above have provided an overview of the different types of cryptographic algorithms, as well as some examples of some available protocols and schemes. Table 3 provides a list of some other noteworthy schemes and cryptosystems employed — or proposed — for a variety of functions, most notably electronic commerce and secure communication. The paragraphs below will show several real cryptographic applications that many of us employ (knowingly or not) everyday for password protection and private communication. Some of the schemes described below never were widely deployed but are still historically interesting, thus remain included here. This list is, by no means, exhaustive but describes items that are of significant current and/or historic importance (a subjective judgement, to be sure).

<table><caption><b>TABLE 3. Other Crypto Algorithms and Systems of Note.</b></caption><tbody><tr><td><a href="https://en.wikipedia.org/wiki/Bitmessage" target="_blank">Bitmessage</a></td><td>A decentralized, encrypted, peer-to-peer, trustless communications protocol for message exchange. The decentralized design, outlined in <a href="https://bitmessage.org/bitmessage.pdf" target="_blank">"Bitmessage: A Peer-to-Peer Message Authentication and Delivery System"</a> (Warren, 2012), is conceptually based on the Bitcoin model.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Capstone_%28cryptography%29" target="_blank">Capstone</a></td><td>A now-defunct U.S. National Institute of Standards and Technology (NIST) and National Security Agency (NSA) project under the Bush Sr. and Clinton administrations for publicly available strong cryptography with keys escrowed by the government (NIST and the Treasury Dept.). Capstone included one or more tamper-proof computer chips for implementation (Clipper), a secret key encryption algorithm (Skipjack), digital signature algorithm (DSA), key exchange algorithm (KEA), and hash algorithm (SHA).</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Challenge-Handshake_Authentication_Protocol" target="_blank">Challenge-Handshake Authentication Protocol (CHAP)</a></td><td>An authentication scheme that allows one party to prove who they are to a second party by demonstrating knowledge of a shared secret without actually divulging that shared secret to a third party who might be listening. Described in <a href="https://www.rfc-editor.org/info/rfc1994" target="_blank">RFC 1994</a>.</td></tr><tr><td><a href="http://www.gpsexpert.net/chimera-specification" target="_blank">Chips-Message Robust Authentication (CHIMERA)</a></td><td>A scheme proposed for authenticating navigation data and the spreading code of civilian signals in the Global Positioning System (GPS). This is an anti-spoofing mechanism to protect the unencrypted civilian signals; GPS military signals are encrypted.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Clipper_chip" target="_blank">Clipper</a></td><td>The computer chip that would implement the Skipjack encryption scheme. The Clipper chip was to have had a deliberate backdoor so that material encrypted with this device would not be beyond the government's reach. Described in 1993, Clipper was dead by 1996. See also EPIC's <a href="http://www.epic.org/crypto/clipper/" target="_blank">The Clipper Chip</a> Web page.</td></tr><tr id="cryptec"><td><a href="https://www.cryptrec.go.jp/en/" target="_blank">Cryptography Research and Evaluation Committees (CRYPTEC)</a></td><td>Similar in concept to the NIST AES process and NESSIE, <a href="https://en.wikipedia.org/wiki/CRYPTREC" target="_blank&quot;">CRYPTEC</a> is the Japanese government's process to evaluate algorithms submitted for government and industry applications. CRYPTEX maintains a list of public key and secret key ciphers, hash functions, MACs, and other crypto algorithms approved for various applications in government environments.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Derived_unique_key_per_transaction" target="_blank">Derived Unique Key Per Transaction (DUKPT)</a></td><td>A key management scheme used for debit and credit card verification with point-of-sale (POS) transaction systems, automated teller machines (ATMs), and other financial applications. In DUKPT, a unique key is derived for each transaction based upon a fixed, shared key in such a way that knowledge of one derived key does not easily yield knowledge of other keys (including the fixed key). Therefore, if one of the derived keys is compromised, neither past nor subsequent transactions are endangered. DUKPT is specified in American National Standard (ANS) ANSI X9.24-1:2009 (<i>Retail Financial Services Symmetric Key Management Part 1: Using Symmetric Techniques</i>) and can be purchased at the <a href="http://webstore.ansi.org/RecordDetail.aspx?sku=ANSI+X9.24-1%3A2009" target="_blank">ANSI X9.24 Web page</a>.</td></tr><tr id="estream"><td><a href="http://www.ecrypt.eu.org/stream/" target="_blank">ECRYPT Stream Cipher Project (eSTREAM)</a></td><td>The <a href="https://en.wikipedia.org/wiki/ESTREAM" target="_blank">eSTREAM</a> project came about as a result of the failure of the <a href="https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#nessie">NESSIE</a> project to produce a stream cipher that survived cryptanalysis. eSTREAM ran from 2004 to 2008 with the primary purpose of promoting the design of efficient and compact stream ciphers. As of September 2008, the eSTREAM suite contains seven ciphers.</td></tr><tr><td><a href="http://itlaw.wikia.com/wiki/Escrowed_Encryption_Standard" target="_blank">Escrowed Encryption Standard (EES)</a></td><td>Largely unused, a controversial crypto scheme employing the SKIPJACK secret key crypto algorithm and a Law Enforcement Access Field (LEAF) creation method. LEAF was one part of the key escrow system and allowed for decryption of ciphertext messages that had been intercepted by law enforcement agencies. Described more in <a href="http://csrc.nist.gov/publications/fips/fips185/fips185.pdf" target="_blank">FIPS PUB 185</a> (archived; no longer in force).</td></tr><tr><td><a href="http://csrc.nist.gov/publications/fips/index.html" target="_blank">Federal Information Processing Standards (FIPS)</a></td><td>These computer security- and crypto-related FIPS PUBs are produced by the U.S. National Institute of Standards and Technology (NIST) as standards for the U.S. Government. <a href="https://www.nist.gov/itl/current-fips" target="_blank">Current Federal Information Processing Standards (FIPS)</a> related to cryptography include:<ul><li><a href="https://www.nist.gov/publications/security-requirements-cryptographic-modules-includes-change-notices-1232002" target="_blank">FIPS PUB 140-2</a>: Security Requirements for Cryptographic Modules</li><li><a href="http://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.180-4.pdf" target="_blank">FIPS PUB 180-4</a>: Secure Hash Standard (SHS)</li><li><a href="http://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf" target="_blank">FIPS PUB 186-4</a>: Digital Signature Standard (DSS)</li><li><a href="https://www.nist.gov/publications/advanced-encryption-standard-aes" target="_blank">FIPS PUB 197</a>: Advanced Encryption Standard (AES)</li><li><a href="https://www.nist.gov/publications/keyed-hash-message-authentication-code-hmac-0" target="_blank">FIPS PUB 198-1</a>: The Keyed-Hash Message Authentication Code (HMAC)</li><li><a href="http://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.202.pdf" target="_blank">FIPS PUB 202</a>: SHA-3 Standard: Permutation-Based Hash and Extendable-Output Functions</li></ul></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Fortezza" target="_blank">Fortezza</a></td><td>A PCMCIA card developed by NSA that implements the Capstone algorithms, intended for use with the Defense Messaging Service (DMS). Originally called <a href="http://www.ieee802.org/11/Documents/DocumentArchives/1994_docs/1194011_scan.pdf" target="_blank">Tessera</a>.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/GOST_%28block_cipher%29" target="_blank">GOST</a></td><td>GOST is a family of algorithms defined in the Russian cryptographic standards. Although most of the specifications are written in Russian, a series of RFCs describe some of the aspects so that the algorithms can be used effectively in Internet applications:<ul><li><a href="https://www.rfc-editor.org/info/rfc4357" target="_blank">RFC 4357</a>: Additional Cryptographic Algorithms for Use with GOST 28147-89, GOST R 34.10-94, GOST R 34.10-2001, and GOST R 34.11-94 Algorithms</li><li><a href="https://www.rfc-editor.org/info/rfc4490" target="_blank">RFC 4490</a>: Using the GOST 28147-89, GOST R 34.11-94, GOST R 34.10-94, and GOST R 34.10-2001 Algorithms with Cryptographic Message Syntax (CMS)</li><li><a href="https://www.rfc-editor.org/info/rfc4491" target="_blank">RFC 4491</a>: Using the GOST R 34.10-94, GOST R 34.10-2001, and GOST R 34.11-94 Algorithms with the Internet X.509 Public Key Infrastructure Certificate and CRL Profile</li><li><a href="https://www.rfc-editor.org/info/rfc5830" target="_blank">RFC 5830</a>: GOST 28147-89: Encryption, Decryption, and Message Authentication Code (MAC) Algorithms</li><li><a href="https://www.rfc-editor.org/info/rfc6986" target="_blank">RFC 6986</a>: GOST R 34.11-2012: Hash Function Algorithm</li><li><a href="https://www.rfc-editor.org/info/rfc7091" target="_blank">RFC 7091</a>: GOST R 34.10-2012: Digital Signature Algorithm (Updates <a href="https://www.rfc-editor.org/info/rfc5832" target="_blank">RFC 5832</a>: GOST R 34.10-2001)</li><li><a href="https://www.rfc-editor.org/info/rfc7801" target="_blank">RFC 7801</a>: GOST R 34.12-2015: Block Cipher "Kuznyechik"</li><li><a href="https://www.rfc-editor.org/info/rfc7836" target="_blank">RFC 7836</a>: Guidelines on the Cryptographic Algorithms to Accompany the Usage of Standards GOST R 34.10-2012 and GOST R 34.11-2012</li><li><a href="https://www.rfc-editor.org/info/rfc8891" target="_blank">RFC 8891</a>: GOST R 34.12-2015: Block Cipher "Magma"</li></ul></td></tr><tr id="tab03-ipsec"><td><a href="https://datatracker.ietf.org/wg/ipsec/charter/" target="_blank">IP Security (IPsec)</a></td><td>The IPsec protocol suite is used to provide privacy and authentication services at the IP layer. An overview of the protocol suite and of the documents comprising IPsec can be found in <a href="https://www.rfc-editor.org/info/rfc2411" target="_blank">RFC 2411</a>. Other documents include:<ul><li><a href="https://www.rfc-editor.org/info/rfc4301" target="_blank">RFC 4301</a>: IP security architecture.</li><li><a href="https://www.rfc-editor.org/info/rfc4302" target="_blank">RFC 4302</a>: IP Authentication Header (AH), one of the two primary IPsec functions; AH provides connectionless integrity and data origin authentication for IP datagrams and protects against replay attacks.</li><li><a href="https://www.rfc-editor.org/info/rfc4303" target="_blank">RFC 4303</a>: IP Encapsulating Security Payload (ESP), the other primary IPsec function; ESP provides a variety of security services within IPsec.</li><li><a href="https://www.rfc-editor.org/info/rfc4304" target="_blank">RFC 4304</a>: Extended Sequence Number (ESN) Addendum, allows for negotiation of a 32- or 64- bit sequence number, used to detect replay attacks.</li><li><a href="https://www.rfc-editor.org/info/rfc4305" target="_blank">RFC 4305</a>: Cryptographic algorithm implementation requirements for ESP and AH.</li><li><a href="https://www.rfc-editor.org/info/rfc5996" target="_blank">RFC 5996</a>: The Internet Key Exchange (IKE) protocol, version 2, providing for mutual authentication and establishing and maintaining security associations.</li><ul><li>IKE v1 was described in three separate documents, <a href="https://www.rfc-editor.org/info/rfc2407" target="_blank">RFC 2407</a> (application of ISAKMP to IPsec), <a href="https://www.rfc-editor.org/info/rfc2408" target="_blank">RFC 2408</a> (ISAKMP, a framework for key management and security associations), and <a href="https://www.rfc-editor.org/info/rfc2409" target="_blank">RFC 2409</a> (IKE, using part of Oakley and part of SKEME in conjunction with ISAKMP to obtain authenticated keying material for use with ISAKMP, and for other security associations such as AH and ESP). IKE v1 is obsoleted with the introduction of IKEv2.</li></ul><li><a href="https://www.rfc-editor.org/info/rfc4307" target="_blank">RFC 4307</a>: Cryptographic algorithms used with IKEv2.</li><li><a href="https://www.rfc-editor.org/info/rfc4308" target="_blank">RFC 4308</a>: Crypto suites for IPsec, IKE, and IKEv2.</li><li><a href="https://www.rfc-editor.org/info/rfc4309" target="_blank">RFC 4309</a>: The use of AES in CBC-MAC mode with IPsec ESP.</li><li><a href="https://www.rfc-editor.org/info/rfc4312" target="_blank">RFC 4312</a>: The use of the Camellia cipher algorithm in IPsec.</li><li><a href="https://www.rfc-editor.org/info/rfc4359" target="_blank">RFC 4359</a>: The Use of RSA/SHA-1 Signatures within Encapsulating Security Payload (ESP) and Authentication Header (AH).</li><li><a href="https://www.rfc-editor.org/info/rfc4434" target="_blank">RFC 4434</a>: Describes AES-XCBC-PRF-128, a pseudo-random function derived from the AES for use with IKE.</li><li><a href="https://www.rfc-editor.org/info/rfc2403" target="_blank">RFC 2403</a>: Describes use of the HMAC with MD5 algorithm for data origin authentication and integrity protection in both AH and ESP.</li><li><a href="https://www.rfc-editor.org/info/rfc2405" target="_blank">RFC 2405</a>: Describes use of DES-CBC (DES in Cipher Block Chaining Mode) for confidentiality in ESP.</li><li><a href="https://www.rfc-editor.org/info/rfc2410" target="_blank">RFC 2410</a>: Defines use of the NULL encryption algorithm (i.e., provides authentication and integrity without confidentiality) in ESP.</li><li><a href="https://www.rfc-editor.org/info/rfc2412" target="_blank">RFC 2412</a>: Describes OAKLEY, a key determination and distribution protocol.</li><li><a href="https://www.rfc-editor.org/info/rfc2451" target="_blank">RFC 2451</a>: Describes use of Cipher Block Chaining (CBC) mode cipher algorithms with ESP.</li><li>RFCs <a href="https://www.rfc-editor.org/info/rfc2522" target="_blank">2522</a> and <a href="https://www.rfc-editor.org/info/rfc2523" target="_blank">2523</a>: Description of Photuris, a session-key management protocol for IPsec.</li></ul><p>In addition, <a href="https://www.rfc-editor.org/info/rfc6379" target="_blank">RFC 6379</a> describes Suite B Cryptographic Suites for IPsec and <a href="https://www.rfc-editor.org/info/rfc6380" target="_blank">RFC 6380</a> describes the Suite B profile for IPsec.</p><p>IPsec was first proposed for use with IP version 6 (IPv6), but can also be employed with the current IP version, IPv4.</p><p>(See more detail about IPsec below in <a href="https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#ipsec">Section 5.6</a>.)</p></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Internet_Security_Association_and_Key_Management_Protocol" target="_blank">Internet Security Association and Key Management Protocol (ISAKMP/OAKLEY)</a></td><td>ISAKMP/OAKLEY provide an infrastructure for Internet secure communications. ISAKMP, designed by the National Security Agency (NSA) and described in <a href="https://www.rfc-editor.org/info/rfc2408" target="_blank">RFC 2408</a>, is a framework for key management and security associations, independent of the key generation and cryptographic algorithms actually employed. The OAKLEY Key Determination Protocol, described in <a href="https://www.rfc-editor.org/info/rfc2412" target="_blank">RFC 2412</a>, is a key determination and distribution protocol using a variation of Diffie-Hellman.</td></tr><tr><td><a href="http://web.mit.edu/kerberos/#what_is" target="_blank">Kerberos</a></td><td>A secret key encryption and authentication system, designed to authenticate requests for network resources within a user domain rather than to authenticate messages. Kerberos also uses a trusted third-party approach; a client communications with the Kerberos server to obtain "credentials" so that it may access services at the application server. Kerberos V4 used DES to generate keys and encrypt messages; Kerberos V5 uses DES and other schemes for key generation.<p>Microsoft added support for Kerberos V5 — with some proprietary extensions — in Windows 2000 Active Directory. There are many Kerberos articles posted at Microsoft's Knowledge Base, notably "<a href="https://msdn.microsoft.com/en-us/library/bb742516.aspx" target="_blank">Kerberos Explained</a>."</p></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Hash-based_message_authentication_code" target="_blank">Keyed-Hash Message Authentication Code (HMAC)</a></td><td>A message authentication scheme based upon secret key cryptography and the secret key shared between two parties rather than public key methods. Described in <a href="http://csrc.nist.gov/publications/fips/fips198/fips-198a.pdf" target="_blank">FIPS PUB 198</a> and <a href="https://www.rfc-editor.org/info/rfc2104" target="_blank">RFC 2104</a>. (See <a href="https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#hmac">Section 5.19 below</a> for details on HMAC operation.)</td></tr><tr><td><a href="http://web.textfiles.com/software/sfs7.txt" target="_blank">Message Digest Cipher (MDC)</a></td><td>Invented by Peter Gutman, MDC turns a one-way hash function into a block cipher.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/MIME_Object_Security_Services" target="_blank">MIME Object Security Services (MOSS)</a></td><td>Designed as a successor to PEM to provide PEM-based security services to MIME messages. Described in <a href="https://tools.ietf.org/html/rfc1848" target="_blank">RFC 1848</a>. Never widely implemented and now defunct.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Mujahedeen_Secrets" target="_blank">Mujahedeen Secrets</a></td><td>A Windows GUI, PGP-like cryptosystem. Developed by supporters of Al-Qaeda, the program employs the five finalist AES algorithms, namely, MARS, RC6, Rijndael, Serpent, and Twofish. Also described in <a href="https://jihadology.net/2010/06/30/al-qaidah-in-the-arabian-peninsula-releases-it-first-english-language-magazine-inspire/" target="_blank"><i>Inspire Magazine</i></a>, Issue 1, pp. 41-44 and <a href="https://jihadology.net/2010/10/11/al-qaidah-in-the-arabian-peninsulas-al-mala%e1%b8%a5im-media-releases-inspire-magazine-issue-2/" target="_blank"><i>Inspire Magazine</i></a>, Issue 2, pp. 58-59. Additional related information can also be found in "<a href="https://www.recordedfuture.com/al-qaeda-encryption-technology-part-2/" target="_blank">How Al-Qaeda Uses Encryption Post-Snowden (Part 2)</a>."</td></tr><tr id="nessie"><td><a href="https://www.cosic.esat.kuleuven.be/nessie/" target="_blank">New European Schemes for Signatures, Integrity and Encryption (NESSIE)</a></td><td><a href="https://en.wikipedia.org/wiki/NESSIE" target="_blank">NESSIE</a> was an independent project meant to augment the work of NIST during the AES adoption process by putting out an open call for new cryptographic primitives. The NESSIE project ran from about 2000-2003. While several new block cipher, PKC, MAC, and digital signature algorithms were found during the NESSIE process, no new stream cipher survived cryptanalysis. As a result, the ECRYPT Stream Cipher Project (<a href="http://www.ecrypt.eu.org/stream/" target="_blank">eSTREAM</a>) was created.</td></tr><tr><td><a href="https://csrc.nist.gov/CSRC/media/Events/ISPAB-MARCH-2006-MEETING/documents/E_Barker-March2006-ISPAB.pdf" target="_blank">NSA Suite B Cryptography</a></td><td>An NSA standard for securing information at the SECRET level. Defines use of:<ul><li>Advanced Encryption Standard (AES) with key sizes of 128 and 256 bits, per <a href="http://csrc.nist.gov/publications/fips/fips197/fips-197.pdf" target="_blank">FIPS PUB 197</a> for encryption</li><li>The Ephemeral Unified Model and the One-Pass Diffie Hellman (referred to as ECDH) using the curves with 256 and 384-bit prime moduli, per <a href="http://csrc.nist.gov/groups/ST/toolkit/documents/SP800-56Arev1_3-8-07.pdf" target="_blank">NIST Special Publication 800-56A</a> for key exchange</li><li>Elliptic Curve Digital Signature Algorithm (ECDSA) using the curves with 256 and 384-bit prime moduli, per <a href="http://csrc.nist.gov/publications/fips/fips186-3/fips_186-3.pdf" target="_blank">FIPS PUB 186-3</a> for digital signatures</li><li>Secure Hash Algorithm (SHA) using 256 and 384 bits, per <a href="http://csrc.nist.gov/publications/fips/fips180-3/fips180-3_final.pdf" target="_blank">FIPS PUB 180-3</a> for hashing</li></ul><p><a href="https://www.rfc-editor.org/info/rfc6239" target="_blank">RFC 6239</a> describes Suite B Cryptographic Suites for Secure Shell (SSH) and <a href="https://www.rfc-editor.org/info/rfc6379" target="_blank">RFC 6379</a> describes Suite B Cryptographic Suites for Secure IP (IPsec).</p><p><a href="https://www.rfc-editor.org/info/rfc8423" target="_blank">RFC 8423</a> reclassifies the RFCs related to the Suite B cryptographic algorithms as Historic, and it discusses the reasons for doing so.</p></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Pretty_Good_Privacy" target="_blank">Pretty Good Privacy (PGP)</a></td><td>A family of cryptographic routines for e-mail, file, and disk encryption developed by Philip Zimmermann. PGP 2.6.x uses RSA for key management and digital signatures, IDEA for message encryption, and MD5 for computing the message's hash value; more information can also be found in <a href="https://www.rfc-editor.org/info/rfc1991" target="_blank">RFC 1991</a>. PGP 5.x (formerly known as "PGP 3") uses Diffie-Hellman/DSS for key management and digital signatures; IDEA, CAST, or 3DES for message encryption; and MD5 or SHA for computing the message's hash value. OpenPGP, described in <a href="https://www.rfc-editor.org/info/rfc9580" target="_blank">RFC 9580</a>, is an open definition of security software based on PGP 5.x. The <a href="https://www.gnupg.org/" target="_blank">GNU Privacy Guard (GPG)</a> is a <i>free software</i> version of OpenPGP.<p>(See more detail about PGP below in <a href="https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#pgp">Section 5.5</a>.)</p></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Privacy-enhanced_Electronic_Mail" target="_blank">Privacy Enhanced Mail (PEM)</a></td><td>An IETF standard for secure electronic mail over the Internet, including provisions for encryption (DES), authentication, and key management (DES, RSA). Developed by the IETF but never widely used. Described in the following RFCs:<ul><li><a href="https://www.rfc-editor.org/info/rfc1421" target="_blank">RFC 1421</a>: Part I, Message Encryption and Authentication Procedures</li><li><a href="https://www.rfc-editor.org/info/rfc1422" target="_blank">RFC 1422</a>: Part II, Certificate-Based Key Management</li><li><a href="https://www.rfc-editor.org/info/rfc1423" target="_blank">RFC 1423</a>: Part III, Algorithms, Modes, and Identifiers</li><li><a href="https://www.rfc-editor.org/info/rfc1424" target="_blank">RFC 1424</a>: Part IV, Key Certification and Related Services</li></ul></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Private_Communications_Technology" target="_blank">Private Communication Technology (PCT)</a></td><td>Developed by <a href="https://tools.ietf.org/html/draft-benaloh-pct-00" target="_blank">Microsoft</a> for secure communication on the Internet. PCT supported Diffie-Hellman, Fortezza, and RSA for key establishment; DES, RC2, RC4, and triple-DES for encryption; and DSA and RSA message signatures. Never widely used; superseded by SSL and TLS.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Secure_Electronic_Transaction" target="_blank">Secure Electronic Transaction (SET)</a></td><td>A communications protocol for securing credit card transactions, developed by MasterCard and VISA, in cooperation with IBM, Microsoft, RSA, and other companies. Merged two other protocols: <a href="http://x5.net/faqs/crypto/q140.html" target="_blank">Secure Electronic Payment Protocol (SEPP)</a>, an open specification for secure bank card transactions over the Internet developed by CyberCash, GTE, IBM, MasterCard, and Netscape; and <a href="http://x5.net/faqs/crypto/q142.html" target="_blank">Secure Transaction Technology (STT)</a>, a secure payment protocol developed by Microsoft and Visa International. Supports DES and RC4 for encryption, and RSA for signatures, key exchange, and public key encryption of bank card numbers. SET V1.0 is described in <a href="http://www.maithean.com/docs/set_bk1.pdf" target="_blank">Book 1</a>, <a href="http://www.maithean.com/docs/set_bk2.pdf" target="_blank">Book 2</a>, and <a href="http://www.maithean.com/docs/set_bk3.pdf" target="_blank">Book 3</a>. SET has been superseded by SSL and TLS.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Secure_Hypertext_Transfer_Protocol" target="_blank">Secure Hypertext Transfer Protocol (S-HTTP)</a></td><td>An extension to HTTP to provide secure exchange of documents over the World Wide Web. Supported algorithms include RSA and Kerberos for key exchange, DES, IDEA, RC2, and Triple-DES for encryption. Described in <a href="https://www.ietf.org/rfc/rfc2660" target="_blank">RFC 2660</a>. S-HTTP was never as widely used as HTTP over SSL (https).</td></tr><tr><td><a href="https://www.zoho.com/mail/help/s-mime.html" target="_blank">Secure Multipurpose Internet Mail Extensions (S/MIME)</a></td><td>An IETF secure e-mail scheme superseding PEM, and adding digital signature and encryption capability to Internet MIME messages. S/MIME Version 3.1 is described in RFCs <a href="https://www.rfc-editor.org/info/rfc3850" target="_blank">3850</a> and <a href="https://www.rfc-editor.org/info/rfc3851" target="_blank">3851</a>, and employs the Cryptographic Message Syntax described in RFCs <a href="https://www.rfc-editor.org/info/rfc3369" target="_blank">3369</a> and <a href="https://www.rfc-editor.org/info/rfc3370" target="_blank">3370</a>.<p>(More detail about S/MIME can be found below in <a href="https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#email">Section 5.15</a>.)</p></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0" target="_blank">Secure Sockets Layer (SSL)</a></td><td>Developed in 1995 by Netscape Communications to provide application-independent security and privacy over the Internet. SSL is designed so that protocols such as HTTP, FTP (File Transfer Protocol), and Telnet can operate over it transparently. SSL allows both server authentication (mandatory) and client authentication (optional). RSA is used during negotiation to exchange keys and identify the actual cryptographic algorithm (DES, IDEA, RC2, RC4, or 3DES) to use for the session. SSL also uses MD5 for message digests and X.509 public key certificates. SSL was found to be breakable soon after the IETF announced formation of group to work on TLS and <a href="https://www.rfc-editor.org/info/rfc6176" target="_blank">RFC 6176</a> specifically prohibits the use of SSL v2.0 by TLS clients. SSL version 3.0 is described in <a href="https://www.rfc-editor.org/info/rfc6101" target="_blank">RFC 6101</a>. All versions of SSL are now deprecated in favor of TLS; TLS v1.0 is sometimes referred to as "SSL v3.1."<p>(More detail about SSL can be found below in <a href="https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#ssl">Section 5.7</a>.)</p></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Server-Gated_Cryptography" target="_blank">Server Gated Cryptography (SGC)</a></td><td><a href="https://technet.microsoft.com/en-us/library/cc737579%28v=ws.10%29.aspx" target="_blank">Microsoft extension to SSL</a> that provided strong encryption for online banking and other financial applications using RC2 (128-bit key), RC4 (128-bit key), DES (56-bit key), or 3DES (equivalent of 168-bit key). Use of SGC required a Windows NT Server running Internet Information Server (IIS) 4.0 with a valid SGC certificate. SGC was available in 32-bit Windows versions of Internet Explorer (IE) 4.0; support for Mac, Unix, and 16-bit Windows versions of IE was planned, but never materialized, and SGC was made moot when browsers started to ship with 128-bit encryption.</td></tr><tr><td>ShangMi (SM) Cipher Suites</td><td>A suite of authentication, encryption, and hash algorithms from the People's Republic of China.<br><ul><li>SM2 Cryptography Algorithm: A public key crypto scheme based on elliptic curves. An overview of the specification, in Chinese, can be found in <a href="http://www.gmbz.org.cn/main/viewfile/2018011001400692565.html" target="_blank">GM/T 0009-2012</a>. Additional specifications can be found in:</li><ul><li><a href="https://www.chinesestandard.net/PDF/English.aspx/GBT32918.1-2016" target="_blank">GB/T 32918.1-2016</a>, Part 1: General</li><li><a href="http://www.gmbz.org.cn/upload/2018-07-24/1532401673138056311.pdf" target="_blank">GB/T 32918.2-2016</a>, Part 2: Digital signature algorithm</li><li><a href="https://www.chinesestandard.net/PDF/English.aspx/GBT32918.3-2016" target="_blank">GB/T 32918.3-2016</a>, Part 3: Key exchange protocol</li><li><a href="https://www.chinesestandard.net/PDF/English.aspx/GBT32918.4-2016" target="_blank">GB/T 32918.4-2016</a>, Part 4: Public key encryption algorithm</li><li><a href="http://www.gmbz.org.cn/upload/2018-07-24/1532401863206085511.pdf" target="_blank">GB/T 32918.5-2017</a>, Part 5: Parameter definition</li></ul><li>SM3 Cryptographic Hash Algorithm: A hash algorithm operating on 512-bit blocks to produce a 256-bit hash value. Described in <a href="http://www.gmbz.org.cn/upload/2018-07-24/1532401392982079739.pdf" target="_blank">GB/T 32905-2016</a>.</li><li>SM4 Block Cipher Algorithm: A Feistel block cipher algorithm with a block length and key length of 128 bits, and 32 rounds. Described in <a href="http://www.gmbz.org.cn/upload/2018-04-04/1522788048733065051.pdf" target="_blank">GB/T 32907-2016</a>.</li></ul>An application of the ShangMi Cipher Suites in TLS can be found in <a href="https://www.rfc-editor.org/info/rfc8998" target="-blank">RFC 8998</a>.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Signal_Protocol" target="_blank">Signal Protocol</a></td><td>A protocol for providing end-to-end encryption for voice calls, video calls, and instant messaging (including group chats). Employing a combination of AES, ECC, and HMAC algorithms, it offers such features as confidentiality, integrity, authentication, forward/future secrecy, and message repudiation. Signal is particularly interesting because of its lineage and widespread use. The Signal Protocol's earliest versions were known as TextSecure, first developed by Open Whisper Systems in 2013. TextSecure itself was based on a 2004 protocol called <a href="https://en.wikipedia.org/wiki/Off-the-Record_Messaging" target="_blank">Off-the-Record (OTR) Messaging</a>, designed as an improvement over OpenPGP and S/MIME. TextSecure v2 (2014) introduced a scheme called the Axolotl Ratchet for key exchange and added additional communication features. After <a href="https://en.wikipedia.org/wiki/Signal_(software)" target="_blank">subsequent iterations</a> improving key management (and the renaming of the key exchange protocol to Double Ratchet), additional cryptographic primitives, and the addition of an encrypted voice calling application (RedPhone), TextSecure was renamed Signal Protocol in 2016. The Ratchet key exchange algorithm is at the heart of the power of this system. Most messaging apps employ the users' public and private keys; the weakness here is that if the phone falls into someone else's hands, all of the messages on the device — including deleted messages — can be decrypted. The Ratchet algorithm generates a set of so-called "temporary keys" for each user, based upon that user's public/private key pair. When two users exchange messages, the Signal protocol creates a secret key by combining the temporary and permanent pairs of public and private keys for both users. Each message is assigned its own secret key. Because the generation of the secret key requires access to both users' private keys, it exists only on their two devices. The Signal Protocol is/has been employed in:<br><ul><li>WhatsApp (introduced 2014)</li><li>G Data Software's Secure Chat (introduced 2015; service discontinued 2018)</li><li>Google's Allo app (introduced 2016; discontinued in favor of Messages app, 2019)</li><li>Facebook Messenger (introduced 2016)</li><li>Skype's Private Conversations mode (introduced 2018)</li><li>All of Google's Rich Communication Services (RCS) on Android systems (introduced 2020)</li></ul>A reasonably good writeup of the protocol can be found in "Demystifying the Signal Protocol for End-to-End Encryption (E2EE)" by <a href="https://blog.cloudboost.io/demystifying-the-signal-protocol-for-end-to-end-encryption-e2ee-3e31830c456f" target="_blank">Kozhukhovskaya</a>, <a href="https://medium.com/@justinomora/demystifying-the-signal-protocol-for-end-to-end-encryption-e2ee-ad6a567e6cb4" target="_blank">Mora</a>, and <a href="https://medium.com/@janelle.wg/demystifying-the-signal-protocol-for-end-to-end-encryption-c93250e6043e" target="_blank">Wong</a> (2017).</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer" target="_blank">Simple Authentication and Security Layer (SASL)</a></td><td>A framework for providing authentication and data security services in connection-oriented protocols (a la TCP), described in <a href="https://www.rfc-editor.org/info/rfc4422" target="_blank">RFC 4422</a>. It provides a structured interface and allows new protocols to reuse existing authentication mechanisms and allows old protocols to make use of new mechanisms.<p>It has been common practice on the Internet to permit anonymous access to various services, employing a plain-text password using a user name of "anonymous" and a password of an email address or some other identifying information. New IETF protocols disallow plain-text logins. The Anonymous SASL Mechanism (<a href="https://www.rfc-editor.org/info/rfc4505" target="_blank">RFC 4505</a>) provides a method for anonymous logins within the SASL framework.</p></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Simple_Key-Management_for_Internet_Protocol" target="_blank">Simple Key-Management for Internet Protocol (SKIP)</a></td><td>Key management scheme for secure IP communication, specifically for IPsec, and designed by Aziz and Diffie. SKIP essentially defines a public key infrastructure for the Internet and even uses X.509 certificates. Most public key cryptosystems assign keys on a per-session basis, which is inconvenient for the Internet since IP is connectionless. Instead, SKIP provides a basis for secure communication between any pair of Internet hosts. SKIP can employ DES, 3DES, IDEA, RC2, RC5, MD5, and SHA-1. As it happened, SKIP was not adopted for IPsec; <a href="https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#tab03-ipsec">IKE</a> was selected instead.</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/SM9_(cryptography_standard)" target="_blank">SM9</a></td><td>Chinese Standard <i>GM/T0044-2016 SM9</i> (2016) is the Chinese national standard for Identity Based Cryptography. SM9 comprises three cryptographic algorithms, namely the <i>Identity Based Digital Signature Algorithm</i>, <i>Identity Based Key Agreement Algorithm</i>, and <i>Identity Based Key Encapsulation Algorithm</i> (allowing one party to securely send a symmetric key to another party). The SM9 scheme is also described in <a href="https://eprint.iacr.org/2017/117.pdf" target="_blank"><i>The SM9 Cryptographic Schemes</i></a> (Z. Cheng).</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Telegram_(software)" target="_blank">Telegram</a></td><td>Telegram, launched in 2013, is a cloud-based instant messaging and voice over IP (VoIP) service, with client app software available for all major computer and mobile device operating systems. Telegram allows users to exchange messages, photos, videos, etc., and supplies end-to-end encryption using a protocol called MTProto. stickers, audio and files of any type. MTProto employs 256-bit AES, 2048-bit RSA, and Diffie-Hellman key exchange. There have been several controversies with Telegram, not the least of which has to do with the nationality of the founders and the true location of the business, as well as some operation issues. From a cryptological viewpoint, however, one cautionary tale can be found in <a href="https://eprint.iacr.org/2015/1177.pdf" target="_blank">"On the CCA (in)security of MTProto"</a> (Jakobsen &amp; Orlandi, 2015), who describe some of the crypto weaknesses of the protocol; specifically, that "MTProto does not satisfy the definitions of authenticated encryption (AE) or indistinguishability under chosen-ciphertext attack (IND-CCA)" (p. 1).</td></tr><tr><td><a href="https://en.wikipedia.org/wiki/Tcpcrypt" target="_blank">Transmission Control Protocol (TCP) encryption (tcpcrypt)</a></td><td>As of 2019, the majority of Internet TCP traffic is not encrypted. The two primary reasons for this are (1) many legacy protocols have no mechanism with which to employ encryption (e.g., without a command such as STARTSSL, the protocol cannot invoke use of any encryption) and (2) many legacy applications cannot be upgraded, so no new encryption can be added. The response from the IETF's <a href="https://datatracker.ietf.org/wg/tcpinc/charter/" target="_blank">TCP Increased Security Working Group</a> was to define a transparent way within the transport layer (i.e., TCP) with which to invoke encryption. The TCP Encryption Negotiation Option (TCP-ENO) addresses these two problems with an out-of-band, fully backward-compatible TCP option with which to negotiate use of encryption. TCP-ENO is described in <a href="https://www.rfc-editor.org/info/rfc8547" target="_blank">RFC 8547</a> and <i>tcpcrypt</i>, an encryption protocol to protect TCP streams, is described in <a href="https://www.rfc-editor.org/info/rfc8548" target="_blank">RFC 8548</a>.</td></tr><tr><td><a href="https://datatracker.ietf.org/wg/tls/charter/" target="_blank">Transport Layer Security (TLS)</a></td><td>TLS v1.0 is an IETF specification (<a href="https://www.rfc-editor.org/info/rfc2246" target="_blank">RFC 2246</a>) intended to replace SSL v3.0. TLS v1.0 employs Triple-DES (secret key cryptography), SHA (hash), Diffie-Hellman (key exchange), and DSS (digital signatures). TLS v1.0 was vulnerable to attack and updated by v1.1 (<a href="https://www.rfc-editor.org/info/rfc4346" target="_blank">RFC 4346</a>), which is now classified as an HISTORIC specification. TLS v1.1 was replaced by TLS v1.2 (<a href="https://www.rfc-editor.org/info/rfc5246" target="_blank">RFC 5246</a>) and, subsequently, by v1.3 (<a href="https://www.rfc-editor.org/info/rfc8446" target="_blank">RFC 8446</a>).<p>TLS is designed to operate over TCP. The IETF developed the Datagram Transport Layer Security (DTLS) protocol to operate over the User Datagram Protocol (UDP). DTLS v1.3 is described in <a href="https://www.rfc-editor.org/info/rfc9147" target="_blank">RFC 9147</a>.</p><p>(See more detail about TLS below in <a href="https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#ssl">Section 5.7</a>.)</p></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/TrueCrypt" target="_blank">TrueCrypt</a></td><td>Open source, multi-platform cryptography software that can be used to encrypt a file, partition, or entire disk. One of TrueCrypt's more interesting features is that of <i>plausible deniability</i> with hidden volumes or hidden operating systems. The original Web site, <a href="http://truecrypt.sourceforge.net/" target="_blank"><i>truecrypt.org</i>,</a> suddenly went dark in May 2014. The current fork of TrueCrypt is <a href="https://www.veracrypt.fr/en/Home.html" target="_blank">VeraCrypt</a>.<p>(See more detail about TrueCrypt below in <a href="https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#tc">Section 5.11</a>.)</p></td></tr><tr><td><a href="https://en.wikipedia.org/wiki/X.509" target="_blank">X.509</a></td><td>ITU-T recommendation for the format of certificates for the public key infrastructure. Certificates map (bind) a user identity to a public key. The IETF application of X.509 certificates is documented in <a href="https://www.rfc-editor.org/info/rfc5280" target="_blank">RFC 5280</a>. An Internet X.509 Public Key Infrastructure is further defined in <a href="https://www.rfc-editor.org/info/rfc4210" target="_blank">RFC 4210</a> (Certificate Management Protocols) and <a href="https://www.rfc-editor.org/info/rfc3647" target="_blank">RFC 3647</a> (Certificate Policy and Certification Practices Framework).</td></tr></tbody></table>

#### Password Protection

Nearly all modern multiuser computer and network operating systems employ passwords at the very least to protect and authenticate users accessing computer and/or network resources. But passwords are _not_ typically kept on a host or server in plaintext, but are generally encrypted using some sort of hash scheme.

```
<b>A) /etc/passwd file</b>

 root:Jbw6BwE4XoUHo:0:0:root:/root:/bin/bash
 carol:FM5ikbQt1K052:502:100:Carol Monaghan:/home/carol:/bin/bash
 alex:LqAi7Mdyg/HcQ:503:100:Alex Insley:/home/alex:/bin/bash
 gary:FkJXupRyFqY4s:501:100:Gary Kessler:/home/gary:/bin/bash
 todd:edGqQUAaGv7g6:506:101:Todd Pritsky:/home/todd:/bin/bash
 josh:FiH0ONcjPut1g:505:101:Joshua Kessler:/home/webroot:/bin/bash

<b>B.1) /etc/passwd file (with shadow passwords)</b>

 root:x:0:0:root:/root:/bin/bash
 carol:x:502:100:Carol Monaghan:/home/carol:/bin/bash
 alex:x:503:100:Alex Insley:/home/alex:/bin/bash
 gary:x:501:100:Gary Kessler:/home/gary:/bin/bash
 todd:x:506:101:Todd Pritsky:/home/todd:/bin/bash
 josh:x:505:101:Joshua Kessler:/home/webroot:/bin/bash

<b>B.2) /etc/shadow file</b>

 root:AGFw$1$P4u/uhLK$l2.HP35rlu65WlfCzq:11449:0:99999:7:::
 carol:kjHaN%35a8xMM8a/0kMl1?fwtLAM.K&amp;kw.:11449:0:99999:7:::
 alex:1$1KKmfTy0a7#3.LL9a8H71lkwn/.hH22a:11449:0:99999:7:::
 gary:9ajlknknKJHjhnu7298ypnAIJKL$Jh.hnk:11449:0:99999:7:::
 todd:798POJ90uab6.k$klPqMt%alMlprWqu6$.:11492:0:99999:7:::
 josh:Awmqpsui*787pjnsnJJK%aappaMpQo07.8:11492:0:99999:7:::
```

**FIGURE 8: Sample entries in Unix/Linux password files.**

Unix/Linux, for example, uses a well-known hash via its _crypt()_ function. Passwords are stored in the _/etc/passwd_ file (Figure 8A); each record in the file contains the username, hashed password, user's individual and group numbers, user's name, home directory, and shell program; these fields are separated by colons (:). Note that each password is stored as a 13-byte string. The first two characters are actually a _salt_, randomness added to each password so that if two users have the same password, they will still be encrypted differently; the salt, in fact, provides a means so that a single password might have 4096 different encryptions. The remaining 11 bytes are the password hash, calculated using DES.

As it happens, the _/etc/passwd_ file is world-readable on Unix systems. This fact, coupled with the weak encryption of the passwords, resulted in the development of the _shadow password_ system where passwords are kept in a separate, non-world-readable file used in conjunction with the normal password file. When shadow passwords are used, the password entry in _/etc/passwd_ is replaced with a "\*" or "x" (Figure 8B.1) and the MD5 hash of the passwords are stored in _/etc/shadow_ along with some other account information (Figure 8B.2).

Windows NT uses a similar scheme to store passwords in the Security Access Manager (SAM) file. In the NT case, all passwords are hashed using the MD4 algorithm, resulting in a 128-bit (16-byte) hash value (they are then _obscured_ using an undocumented mathematical transformation that was a secret until distributed on the Internet). The password password, for example, might be stored as the hash value (in hexadecimal) 60771b22d73c34bd4a290a79c8b09f18.

Passwords are not saved in plaintext on computer systems precisely so they cannot be easily compromised. For similar reasons, we don't want passwords sent in plaintext across a network. But for remote logon applications, how does a client system identify itself or a user to the server? One mechanism, of course, is to send the password as a hash value and that, indeed, may be done. A weakness of that approach, however, is that an intruder can grab the password off of the network and use an off-line attack (such as a _dictionary attack_ where an attacker takes every known word and encrypts it with the network's encryption algorithm, hoping eventually to find a match with a purloined password hash). In some situations, an attacker only has to copy the hashed password value and use it later on to gain unauthorized entry without ever learning the actual password.

An even stronger authentication method uses the password to modify a shared secret between the client and server, but never allows the password in any form to go across the network. This is the basis for the Challenge Handshake Authentication Protocol (CHAP), the remote logon process used by Windows NT.

As suggested above, Windows NT passwords are stored in a security file on a server as a 16-byte hash value. In truth, Windows NT stores _two_ hashes; a weak hash based upon the old LAN Manager (LanMan) scheme and the newer NT hash. When a user logs on to a server from a remote workstation, the user is identified by the username, sent across the network in plaintext (no worries here; it's not a secret anyway!). The server then generates a 64-bit random number and sends it to the client (also in plaintext). This number is the _challenge_.

Using the LanMan scheme, the client system then encrypts the challenge using DES. Recall that DES employs a 56-bit key, acts on a 64-bit block of data, and produces a 64-bit output. In this case, the 64-bit data block is the random number. The client actually uses three different DES keys to encrypt the random number, producing three different 64-bit outputs. The first key is the first seven bytes (56 bits) of the password's hash value, the second key is the next seven bytes in the password's hash, and the third key is the remaining two bytes of the password's hash concatenated with five zero-filled bytes. (So, for the example above, the three DES keys would be 60771b22d73c34, bd4a290a79c8b0, and 9f180000000000.) Each key is applied to the random number resulting in three 64-bit outputs, which comprise the _response_. Thus, the server's 8-byte challenge yields a 24-byte response from the client and this is all that would be seen on the network. The server, for its part, does the same calculation to ensure that the values match.

There is, however, a significant weakness to this system. Specifically, the response is generated in such a way as to effectively reduce 16-byte hash to three smaller hashes, of length seven, seven, and two, respectively. Thus, a password cracker has to break at most a 7-byte hash. One Windows NT vulnerability test program that I used in the past reported passwords that were "too short," defined as "less than 8 characters." When I asked how the program knew that passwords were too short, the software's salespeople suggested to me that the program broke the passwords to determine their length. This was, in fact, not the case at all; all the software really had to do was to look at the last eight bytes of the Windows NT LanMan hash to see that the password was seven or fewer characters.

Consider the following example, showing the LanMan hash of two different short passwords (take a close look at the last 8 bytes):

<table><tbody><tr><td>AA:</td><td><span face="courier">89D42A44E77140AAAAD3B435B51404EE</span></td></tr><tr><td>AAA:</td><td><span face="courier">1C3A2B6D939A1021AAD3B435B51404EE</span></td></tr></tbody></table>

Note that the NT hash provides no such clue:

<table><tbody><tr><td>AA:</td><td><span face="courier">C5663434F963BE79C8FD99F535E7AAD8</span></td></tr><tr><td>AAA:</td><td><span face="courier">6B6E0FB2ED246885B98586C73B5BFB77</span></td></tr></tbody></table>

It is worth noting that the discussion above describes the Microsoft version of CHAP, or MS-CHAP (MS-CHAPv2 is described in [RFC 2759](https://www.rfc-editor.org/info/rfc2759)). MS-CHAP assumes that it is working with hashed values of the password as the key to encrypting the challenge. More traditional CHAP ([RFC 1994](https://www.rfc-editor.org/info/rfc1994)) assumes that it is starting with passwords in plaintext. The relevance of this observation is that a CHAP client, for example, cannot be authenticated by an MS-CHAP server; both client and server must use the same CHAP version.

#### Diffie-Hellman Key Exchange

Diffie and Hellman introduced the concept of public key cryptography. The mathematical "trick" of Diffie-Hellman key exchange is that it is relatively easy to compute exponents compared to computing discrete logarithms. Diffie-Hellman allows two parties — the ubiquitous Alice and Bob — to generate a secret key; they need to exchange some information over an unsecure communications channel to perform the calculation but an eavesdropper cannot determine the shared secret key based upon this information.

Diffie-Hellman works like this. Alice and Bob start by agreeing on a large prime number, N. They also have to choose some number G so that G<N.

There is actually another constraint on G, namely that it must be primitive with respect to N. _Primitive_ is a definition that is a little beyond the scope of our discussion but basically G is primitive to N if the set of N-1 values of G<sup>i</sup> mod N for i = (1,N-1) are all different. As an example, 2 is not primitive to 7 because the set of powers of 2 from 1 to 6, mod 7 (i.e., 2<sup>1</sup> mod 7, 2<sup>2</sup> mod 7, ..., 2<sup>6</sup> mod 7) = {2,4,1,2,4,1}. On the other hand, 3 is primitive to 7 because the set of powers of 3 from 1 to 6, mod 7 = {3,2,6,4,5,1}.

(The definition of primitive introduced a new term to some readers, namely _mod_. The phrase _x mod y_ (and read as written!) means "take the remainder after dividing x by y." Thus, 1 mod 7 = 1, 9 mod 6 = 3, and 8 mod 8 = 0. Read more about the [_modulo_ function](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#modulo) in the appendix.)

Anyway, either Alice or Bob selects N and G; they then tell the other party what the values are. Alice and Bob then work independently (Figure 9):

**FIGURE 9: Diffie-Hellman key exchange model.**

Note that X<sub>A</sub> and X<sub>B</sub> are kept secret while Y<sub>A</sub> and Y<sub>B</sub> are openly shared; these are the private and public keys, respectively. Based on their own private key and the public key learned from the other party, Alice and Bob have computed their secret keys, K<sub>A</sub> and K<sub>B</sub>, respectively, which are equal to G<sup><i>X<sub>A</sub>X<sub>B</sub></i></sup> mod N.

Perhaps a small example will help here. Although Alice and Bob will really choose large values for N and G, I will use small values for example only; let's use N=7 and G=3, as shown in Figure 10.

**FIGURE 10: Diffie-Hellman key exchange example.**

In this example, then, Alice and Bob will both find the secret key 1 which is, indeed, 3<sup>6</sup> mod 7 (i.e., G<sup><i>X<sub>A</sub>X<sub>B</sub></i></sup> = 3<sup><i>2x3</i></sup>). If an eavesdropper (Eve) was listening in on the information exchange between Alice and Bob, she would learn G, N, Y<sub>A</sub>, and Y<sub>B</sub> which is a lot of information but insufficient to compromise the key; as long as X<sub>A</sub> and X<sub>B</sub> remain unknown, K is safe. As stated above, calculating Y = G<sup><i>X</i></sup> is a lot easier than finding X = log<sub>G</sub> Y.

___

> **A short digression on modulo arithmetic.** In the paragraph above, we noted that 3<sup>6</sup> mod 7 = 1. This can be confirmed, of course, by noting that:
> 
> 3<sup>6</sup> = 729 = 104\*7 + 1
> 
> There is a nice property of modulo arithmetic, however, that makes this determination a little easier, namely: (a mod x)(b mod x) = (ab mod x). Therefore, one possible shortcut is to note that 3<sup>6</sup> = (3<sup>3</sup>)(3<sup>3</sup>). Therefore, 3<sup>6</sup> mod 7 = (3<sup>3</sup> mod 7)(3<sup>3</sup> mod 7) = (27 mod 7)(27 mod 7) = 6\*6 mod 7 = 36 mod 7 = 1.

___

Diffie-Hellman can also be used to allow key sharing amongst multiple users. Note again that the Diffie-Hellman algorithm is used to generate secret keys, not to encrypt and decrypt messages.

#### RSA Public Key Cryptography

Unlike Diffie-Hellman, RSA can be used for key exchange as well as digital signatures and the encryption of small blocks of data. Today, RSA is primarily used to encrypt the session key used for secret key encryption (message integrity) or the message's hash value (digital signature). RSA's mathematical hardness comes from the ease in calculating large numbers and the difficulty in finding the prime factors of those large numbers. Although employed with numbers using hundreds of digits, the math behind RSA is relatively straight-forward.

To create an RSA public/private key pair, here are the basic steps:

1.  Choose two prime numbers, p and q. From these numbers you can calculate the modulus, n = pq.
2.  Select a third number, e, that is relatively prime to (i.e., it does not divide evenly into) the product (p-1)(q-1). The number e is the public exponent.
3.  Calculate an integer d from the quotient (ed-1)/\[(p-1)(q-1)\]. The number d is the private exponent.

The public key is the number pair (n,e). Although these values are publicly known, it is computationally infeasible to determine d from n and e if p and q are large enough.

To encrypt a message, M, with the public key, create the ciphertext, C, using the equation:

C = M<sup>e</sup> mod n

The receiver then decrypts the ciphertext with the private key using the equation:

M = C<sup>d</sup> mod n

Now, this might look a bit complex and, indeed, the mathematics does take a lot of computer power given the large size of the numbers; since p and q may be 100 digits (decimal) or more, d and e will be about the same size and n may be over 200 digits. Nevertheless, a simple example may help. In this example, the values for p, q, e, and d are purposely chosen to be very small and the reader will see exactly how badly these values perform, but hopefully the algorithm will be adequately demonstrated:

1.  Select p=3 and q=5.
2.  The modulus n = pq = 15.
3.  The value e must be relatively prime to (p-1)(q-1) = (2)(4) = 8. Select e=11.
4.  The value d must be chosen so that (ed-1)/\[(p-1)(q-1)\] is an integer. Thus, the value (11d-1)/\[(2)(4)\] = (11d-1)/8 must be an integer. Calculate one possible value, d=3.
5.  Let's suppose that we want to send a message — maybe a secret key — that has the numeric value of 7 (i.e., M=7). \[More on this choice below.\]
6.  The sender encrypts the message (M) using the public key value (e,n)=(11,15) and computes the ciphertext (C) with the formula C = 7<sup>11</sup> mod 15 = 1977326743 mod 15 = 13.
7.  The receiver decrypts the ciphertext using the private key value (d,n)=(3,15) and computes the plaintext with the formula M = 13<sup>3</sup> mod 15 = 2197 mod 15 = 7.

I choose this trivial example because the value of n is so small (in particular, the value M cannot exceed n). But here is a more realistic example using larger d, e, and n values, as well as a more meaningful message; thanks to Barry Steyn for permission to use values from his [How RSA Works With Examples](http://doctrina.org/How-RSA-Works-With-Examples.html) page.

Let's say that we have chosen p and q so that we have the following value for n:

> 14590676800758332323018693934907063529240187237535716439958187  
> 10198734387990053589383695714026701498021218180862924674228281  
> 57022922076746906543401224889672472407926969987100581290103199  
> 31785875366371086235765651050788371429711563734278891146353510  
> 2712032765166518411726859837988672111837205085526346618740053

Let's also suppose that we have selected the public key, e, and private key, d, as follows:

> 65537
> 
> 89489425009274444368228545921773093919669586065884257445497854  
> 45648767483962981839093494197326287961679797060891728367987549  
> 93315741611138540888132754881105882471930775825272784379065040  
> 15680623423550067240042466665654232383502922215493623289472138  
> 866445818789127946123407807725702626644091036502372545139713

Now suppose that our message (M) is the character string "attack at dawn" which has the numeric value (after converting the ASCII characters to a bit string and interpreting that bit string as a decimal number) of 1976620216402300889624482718775150.

The encryption phase uses the formula C = M<sup>e</sup> mod n, so C has the value:

> 35052111338673026690212423937053328511880760811579981620642802  
> 34668581062310985023594304908097338624111378404079470419397821  
> 53784997654130836464387847409523069325349451950801838615742252  
> 26218879827232453912820596886440377536082465681750074417459151  
> 485407445862511023472235560823053497791518928820272257787786

The decryption phase uses the formula M = C<sup>d</sup> mod n, so M has the value that matches our original plaintext:

> 1976620216402300889624482718775150

This more realistic example gives just a clue as to how large the numbers are that are used in the real world implementations. RSA keylengths of 512 and 768 bits are considered to be pretty weak. The minimum suggested RSA key is 1024 bits; 2048 and 3072 bits are even better.

As an aside, Adam Back ([http://www.cypherspace.org/~adam/](http://www.cypherspace.org/~adam/)) wrote a two-line Perl script to implement RSA. It employs dc, an arbitrary precision arithmetic package that ships with most UNIX systems:

```
print pack"C*",split/\D+/,`echo "16iII*o\U@{$/=$z;[(pop,pop,unpack"H*",&lt;&gt;
)]}\EsMsKsN0[lN*1lK[d2%Sa2/d0&lt;X+d*lMLa^*lN%0]dsXx++lMlN/dsM0&lt;J]dsJxp"|dc`
```

#### DES, Breaking DES, and DES Variants

The Data Encryption Standard (DES) started life in the mid-1970s, adopted by the National Bureau of Standards (NBS) \[now the National Institute of Standards and Technology (NIST)\] as Federal Information Processing Standard 46 ([FIPS PUB 46-3](http://csrc.nist.gov/publications/fips/fips46-3/fips46-3.pdf)) and by the American National Standards Institute (ANSI) as X3.92.

As mentioned earlier, DES uses the Data Encryption Algorithm (DEA), a secret key block-cipher employing a 56-bit key operating on 64-bit blocks. [FIPS PUB 81](http://csrc.nist.gov/publications/fips/fips81/fips81.htm) describes four modes of DES operation: Electronic Codebook (ECB), Cipher Block Chaining (CBC), Cipher Feedback (CFB), and Output Feedback (OFB). Despite all of these options, ECB is the most commonly deployed mode of operation.

NIST finally declared DES obsolete in 2004, and withdrew FIPS PUB 46-3, 74, and 81 ([_Federal Register_, July 26, 2004, _69_(142), 44509-44510](https://www.gpo.gov/fdsys/pkg/FR-2004-07-26/html/04-16894.htm)). Although other block ciphers have replaced DES, it is still interesting to see how DES encryption is performed; not only is it sort of neat, but DES was the first crypto scheme commonly seen in non-governmental applications and was the catalyst for modern "public" cryptography and the first public [Feistel cipher](https://en.wikipedia.org/wiki/Feistel_cipher). DES still remains in many products — and cryptography students and cryptographers will continue to study DES for years to come.

**DES Operational Overview**

DES uses a 56-bit key. In fact, the 56-bit key is divided into eight 7-bit blocks and an 8th odd parity bit is added to each block (i.e., a "0" or "1" is added to the block so that there are an odd number of 1 bits in each 8-bit block). By using the 8 parity bits for rudimentary error detection, a DES key is actually 64 bits in length for computational purposes although it only has 56 bits worth of randomness, or _entropy_ (See [Section A.3](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#entropy) for a brief discussion of entropy and information theory).

![](https://www.garykessler.net/library/images/crypto_des.gif)

**FIGURE 11: DES enciphering algorithm.**

DES then acts on 64-bit blocks of the plaintext, invoking 16 rounds of permutations, swaps, and substitutes, as shown in Figure 11. The standard includes tables describing all of the selection, permutation, and expansion operations mentioned below; these aspects of the algorithm are not secrets. The basic DES steps are:

1.  The 64-bit block to be encrypted undergoes an initial permutation (IP), where each bit is moved to a new bit position; e.g., the 1st, 2nd, and 3rd bits are moved to the 58th, 50th, and 42nd position, respectively.
2.  The 64-bit permuted input is divided into two 32-bit blocks, called _left_ and _right_, respectively. The initial values of the left and right blocks are denoted L<sub>0</sub> and R<sub>0</sub>.
3.  There are then 16 rounds of operation on the L and R blocks. During each iteration (where _n_ ranges from 1 to 16), the following formulae apply:
    
    L<sub>n</sub> = R<sub>n-1</sub>  
    R<sub>n</sub> = L<sub>n-1</sub> ⊕ f(R<sub>n-1</sub>,K<sub>n</sub>)
    
    At any given step in the process, then, the new L block value is merely taken from the prior R block value. The new R block is calculated by taking the bit-by-bit exclusive-OR (XOR) of the prior L block with the results of applying the DES cipher function, _f_, to the prior R block and K<sub>n</sub>. (K<sub>n</sub> is a 48-bit value derived from the 64-bit DES key. Each round uses a different 48 bits according to the standard's Key Schedule algorithm.)
    
    The cipher function, f, combines the 32-bit R block value and the 48-bit subkey in the following way. First, the 32 bits in the R block are expanded to 48 bits by an expansion function (E); the extra 16 bits are found by repeating the bits in 16 predefined positions. The 48-bit expanded R-block is then ORed with the 48-bit subkey. The result is a 48-bit value that is then divided into eight 6-bit blocks. These are fed as input into 8 selection (S) boxes, denoted S<sub>1</sub>,...,S<sub>8</sub>. Each 6-bit input yields a 4-bit output using a table lookup based on the 64 possible inputs; this results in a 32-bit output from the S-box. The 32 bits are then rearranged by a permutation function (P), producing the results from the cipher function.
    
4.  The results from the final DES round — i.e., L<sub>16</sub> and R<sub>16</sub> — are recombined into a 64-bit value and fed into an inverse initial permutation (IP<sup>-1</sup>). At this step, the bits are rearranged into their original positions, so that the 58th, 50th, and 42nd bits, for example, are moved back into the 1st, 2nd, and 3rd positions, respectively. The output from IP<sup>-1</sup> is the 64-bit ciphertext block.

Consider this example using DES in CBC mode with the following 56-bit key and input:

Key: **1100101 0100100 1001001 0011101 0110101 0101011 1101100 0011010** = **0x6424491D352B6C1A**

Input character string (ASCII/IA5): **+2903015-08091765**  
Input string (hex): **0x2B323930333031352D3038303931373635**

Output string (hex): **0x9812CB620B2E9FD3AD90DE2B92C6BBB6C52753AC43E1AFA6**  
Output character string (BASE64): **mBLLYgsun9OtkN4rksa7tsUnU6xD4a+m**

Observe that we start with a 17-byte input message. DES acts on eight bytes at a time, so this message is padded to 24 bytes and provides three "inputs" to the cipher algorithm (we don't see the padding here; it is appended by the DES code). Since we have three input blocks, we get 24 bytes of output from the three 64-bit (eight byte) output blocks.

If you want to test this, a really good free, [online DES calculator](http://www.emvlab.org/descalc/) hosted by the Information Security Group at University College London. An excellent step-by-step example of DES can also be found at J. Orlin Grabbe's [_The DES Algorithm Illustrated_](http://page.math.tu-berlin.de/~kant/teaching/hess/krypto-ws2006/des.htm) page.

___

> **NOTE:** You'll notice that the output above is shown in BASE64. BASE64 is a 64-character alphabet — i.e., a six-bit character code composed of upper- and lower-case letters, the digits 0-9, and a few punctuation characters — that is commonly used as a way to display binary data. A byte has eight bits, or 256 values, but not all 256 ASCII characters are defined and/or printable. BASE64, simply, takes a binary string (or file), divides it into six-bit blocks, and translates each block into a printable character. More information about BASE64 can be found at my [BASE64 Alphabet](https://www.garykessler.net/library/base64.html) page or at [Wikipedia](https://en.wikipedia.org/wiki/Base64).

___

**Breaking DES**

The mainstream cryptographic community has long held that DES's 56-bit key was too short to withstand a brute-force attack from modern computers. Remember Moore's Law: computer power doubles every 18 months. Given that increase in power, a key that could withstand a brute-force guessing attack in 1975 could hardly be expected to withstand the same attack a quarter century later.

DES is even more vulnerable to a brute-force attack because it is often used to encrypt words, meaning that the entropy of the 64-bit block is, effectively, greatly reduced. That is, if we are encrypting random bit streams, then a given byte might contain any one of 2<sup>8</sup> (256) possible values and the entire 64-bit block has 2<sup>64</sup>, or about 18.5 quintillion, possible values. If we are encrypting words, however, we are most likely to find a limited set of bit patterns; perhaps 70 or so if we account for upper and lower case letters, the numbers, space, and some punctuation. This means that only about ¼ of the bit combinations of a given byte are likely to occur.

Despite this criticism, the U.S. government insisted throughout the mid-1990s that 56-bit DES was secure and virtually unbreakable if appropriate precautions were taken. In response, RSA Laboratories sponsored a series of [cryptographic challenges](http://www.emc.com/emc-plus/rsa-labs/historical/the-rsa-laboratories-secret-key-challenge.htm) to prove that DES was no longer appropriate for use.

DES Challenge I was launched in March 1997. It was completed in 84 days by R. Verser in a collaborative effort using thousands of computers on the Internet.

The first DES Challenge II lasted 40 days in early 1998. This problem was solved by [distributed.net](http://www.distributed.net/), a worldwide distributed computing network using the spare CPU cycles of computers around the Internet (participants in distributed.net's activities load a client program that runs in the background, conceptually similar to the SETI @Home "Search for Extraterrestrial Intelligence" project). The distributed.net systems were checking 28 _billion_ keys per second by the end of the project.

The second DES Challenge II lasted less than 3 days. On July 17, 1998, the Electronic Frontier Foundation (EFF) announced the construction of hardware that could brute-force a DES key in an average of 4.5 days. Called Deep Crack, the device could check 90 billion keys per second and cost only about $220,000 including design (it was erroneously and widely reported that subsequent devices could be built for as little as $50,000). Since the design is scalable, this suggests that an organization could build a DES cracker that could break 56-bit keys in an average of a day for as little as $1,000,000. Information about the hardware design and all software can be obtained from the [EFF](https://web.archive.org/web/20170507231657/https://w2.eff.org/Privacy/Crypto/Crypto_misc/DESCracker/HTML/19980716_eff_des_faq.html).

The [DES Challenge III](http://www.emc.com/emc-plus/rsa-labs/historical/des-challenge-iii.htm), launched in January 1999, was broken is less than a day by the combined efforts of Deep Crack and distributed.net. This is widely considered to have been the final nail in DES's coffin.

The [Deep Crack algorithm](https://en.wikipedia.org/wiki/EFF_DES_cracker) is actually quite interesting. The general approach that the DES Cracker Project took was not to break the algorithm mathematically but instead to launch a brute-force attack by guessing every possible key. A 56-bit key yields 2<sup>56</sup>, or about 72 quadrillion, possible values. So the DES cracker team looked for any shortcuts they could find! First, they assumed that _some_ recognizable plaintext would appear in the decrypted string even though they didn't have a specific known plaintext block. They then applied all 2<sup>56</sup> possible key values to the 64-bit block (I don't mean to make this sound simple!). The system checked to see if the decrypted value of the block was "interesting," which they defined as bytes containing one of the alphanumeric characters, space, or some punctuation. Since the likelihood of a single byte being "interesting" is about ¼, then the likelihood of the entire 8-byte stream being "interesting" is about ¼<sup>8</sup>, or 1/65536 (½<sup>16</sup>). This dropped the number of possible keys that might yield positive results to about 2<sup>40</sup>, or about a trillion.

They then made the assumption that an "interesting" 8-byte block would be followed by another "interesting" block. So, if the first block of ciphertext decrypted to something interesting, they decrypted the next block; otherwise, they abandoned this key. Only if the second block was also "interesting" did they examine the key closer. Looking for 16 consecutive bytes that were "interesting" meant that only 2<sup>24</sup>, or 16 million, keys needed to be examined further. This further examination was primarily to see if the text made any sense. Note that possible "interesting" blocks might be 1hJ5&aB7 or DEPOSITS; the latter is more likely to produce a better result. And even a slow laptop today can search through lists of only a few million items in a relatively short period of time. (Interested readers are urged to read [_Cracking DES_](https://www.amazon.com/Cracking-Secrets-Encryption-Research-Politics/dp/1565925203) and EFF's [Cracking DES](https://w2.eff.org/Privacy/Crypto/Crypto_misc/DESCracker/HTML/19980716_eff_des_faq.html) page.)

It is well beyond the scope of this paper to discuss other forms of breaking DES and other codes. Nevertheless, it is worth mentioning a couple of forms of cryptanalysis that have been shown to be effective against DES. _Differential cryptanalysis_, invented in 1990 by E. Biham and A. Shamir (of RSA fame), is a chosen-plaintext attack. By selecting pairs of plaintext with particular differences, the cryptanalyst examines the differences in the resultant ciphertext pairs. _Linear plaintext_, invented by M. Matsui, uses a linear approximation to analyze the actions of a block cipher (including DES). Both of these attacks can be more efficient than brute force.

**DES Variants**

Once DES was "officially" broken, several variants appeared. But none of them came overnight; work at hardening DES had already been underway. In the early 1990s, there was a proposal to increase the security of DES by effectively increasing the key length by using multiple keys with multiple passes. But for this scheme to work, it had to first be shown that the DES function is **not** a [_group_](https://en.wikipedia.org/wiki/Group_%28mathematics%29), as defined in mathematics. If DES was a group, then we could show that for two DES keys, X1 and X2, applied to some plaintext (P), we can find a single equivalent key, X3, that would provide the same result; i.e.,

E<sub>X2</sub>(E<sub>X1</sub>(P)) = E<sub>X3</sub>(P)

where E<sub>X</sub>(P) represents DES encryption of some plaintext _P_ using DES key _X_. If DES were a group, it wouldn't matter how many keys and passes we applied to some plaintext; we could always find a single 56-bit key that would provide the same result.

As it happens, DES was proven to not be a group so that as we apply additional keys and passes, the effective key length increases. One obvious choice, then, might be to use two keys and two passes, yielding an effective key length of 112 bits. Let's call this Double-DES. The two keys, Y1 and Y2, might be applied as follows:

C = E<sub>Y2</sub>(E<sub>Y1</sub>(P))  
P = D<sub>Y1</sub>(D<sub>Y2</sub>(C))

where E<sub>Y</sub>(P) and D<sub>Y</sub>(C) represent DES encryption and decryption, respectively, of some plaintext _P_ and ciphertext _C_, respectively, using DES key _Y_.

So far, so good. But there's an interesting attack that can be launched against this "Double-DES" scheme. First, notice that the applications of the formula above can be thought of with the following individual steps (where C' and P' are intermediate results):

C' = E<sub>Y1</sub>(P) and C = E<sub>Y2</sub>(C')  
P' = D<sub>Y2</sub>(C) and P = D<sub>Y1</sub>(P')

Unfortunately, C'=P'. That leaves us vulnerable to a simple _known plaintext_ attack (sometimes called "Meet-in-the-middle") where the attacker knows some plaintext (P) and its matching ciphertext (C). To obtain C', the attacker needs to try all 2<sup>56</sup> possible values of Y1 applied to P; to obtain P', the attacker needs to try all 2<sup>56</sup> possible values of Y2 applied to C. Since C'=P', the attacker knows when a match has been achieved — after only 2<sup>56</sup> + 2<sup>56</sup> = 2<sup>57</sup> key searches, only twice the work of brute-forcing DES. So "Double-DES" is not a good solution.

Triple-DES (3DES), based upon the Triple Data Encryption Algorithm (TDEA), is described in [FIPS PUB 46-3](http://csrc.nist.gov/publications/fips/fips46-3/fips46-3.pdf). 3DES, which is not susceptible to a meet-in-the-middle attack, employs three DES passes and one, two, or three keys called K1, K2, and K3. Generation of the ciphertext (C) from a block of plaintext (P) is accomplished by:

C = E<sub>K3</sub>(D<sub>K2</sub>(E<sub>K1</sub>(P)))

where E<sub>K</sub>(P) and D<sub>K</sub>(P) represent DES encryption and decryption, respectively, of some plaintext _P_ using DES key _K_. (For obvious reasons, this is sometimes referred to as an _encrypt-decrypt-encrypt mode_ operation.)

Decryption of the ciphertext into plaintext is accomplished by:

P = D<sub>K1</sub>(E<sub>K2</sub>(D<sub>K3</sub>(C)))

The use of three, independent 56-bit keys provides 3DES with an effective key length of 168 bits. The specification also defines use of two keys where, in the operations above, K3 = K1; this provides an effective key length of 112 bits. Finally, a third keying option is to use a single key, so that K3 = K2 = K1 (in this case, the effective key length is 56 bits and 3DES applied to some plaintext, P, will yield the same ciphertext, C, as normal DES would with that same key). Given the relatively low cost of key storage and the modest increase in processing due to the use of longer keys, the best recommended practices are that 3DES be employed with three keys.

Another variant of DES, called DESX, is due to Ron Rivest. Developed in 1996, DESX is a very simple algorithm that greatly increases DES's resistance to brute-force attacks without increasing its computational complexity. In DESX, the plaintext input is XORed with 64 additional key bits prior to encryption and the output is likewise XORed with the 64 key bits. By adding just two XOR operations, DESX has an effective keylength of 120 bits against an exhaustive key-search attack. As it happens, DESX is no more immune to other types of more sophisticated attacks, such as differential or linear cryptanalysis, but brute-force is the primary attack vector on DES.

**Closing Comments**

After DES was deprecated and replaced by the Advanced Encryption Standard (AES) because of its vulnerability to a modestly-priced brute-force attack, many applications continued to rely on DES for security, and many software designers and implementers continued to include DES in new applications. In some cases, use of DES made sense but, inevitably, DES was discontinued in production software and hardware. [RFC 4772](https://www.rfc-editor.org/info/rfc4772) — dated December 2006 — discusses the security implications of employing DES, five years after AES had become the official standard.

On a final note, readers may be interested in seeing [an Excel implementation of DES](http://www.nayuki.io/page/des-cipher-internals-in-excel) or J.O. Grabbe's [The DES Algorithm Illustrated](http://page.math.tu-berlin.de/~kant/teaching/hess/krypto-ws2006/des.htm).

#### Pretty Good Privacy (PGP)

Pretty Good Privacy (PGP) is one of today's most widely used public key cryptography programs and was the first open cryptosystem that combined hashing, compression, SKC, and PKC into a method to protect files, devices, and e-mail. Public keys were shared via a concept known as a Web of Trust; individuals would directly exchange their public keyrings and then share their keyrings with other trusted parties. Developed by [Philip Zimmermann](http://www.philzimmermann.com/) in the early 1990s, and the subject of controversy for many years, PGP is available as a plug-in for many e-mail clients, such as Apple Mail (with GPG), Eudora, Gmail, Microsoft Outlook/Outlook Express, Mozilla Thunderbird (with Enigmail), and ProtonMail.

___

> In June 1991, Zimmermann uploaded PGP to the Internet. PGP secret keys, however, were 128 bits or larger, making it a "strong" cryptography product. Export of strong crypto products without a license was a violation of International Traffic in Arms Regulations (ITAR) and, in fact, Zimmermann was the target of an FBI investigation from February 1993 to January 1996. Yet, in 1995, perhaps as a harbinger of the mixed feelings that this technology engendered, the Electronic Frontier Foundation (EFF) awarded Zimmermann the Pioneer Award and _Newsweek Magazine_ named him one of the 50 most influential people on the Internet.

___

PGP can be used to sign or encrypt e-mail messages with the mere click of the mouse. Depending upon the version of PGP, the software uses SHA or MD5 for calculating the message hash; CAST, Triple-DES, or IDEA for encryption; and RSA or DSS/Diffie-Hellman for key exchange and digital signatures.

When PGP is first installed, the user has to create a key-pair. One key, the public key, can be advertised and widely circulated. The private key is protected by use of a _passphrase_. The passphrase has to be entered every time the user accesses their private key.

```
 -----BEGIN PGP SIGNED MESSAGE-----
 Hash: SHA512

 Hi Carol.

 What was that pithy Groucho Marx quote?

 /kess

-----BEGIN PGP SIGNATURE-----
Comment: GPGTools - https://gpgtools.org

iQEcBAEBCgAGBQJYaTDaAAoJEE2ePRsA5fMj9wcH/jje/RBQYKg1ZYq1h52FpS3f
GqnIkKq0wv2KiyCqIilbvb8eo2Fit7sIRo5AO3FJ9qIgRHvet+8pnRboks3uTYTM
euNctkTOxcECZHupexdfB/5j5kGLn8UytIpHMa/Th4LKqvh+a6fU4nlCXe1qRSDq
7HUAvtG03LhPoAoVS411+wI+UtUf1+xvHLRJeKnhgi5j/d9tbc+K5rhPr8Bqb4Kz
oHkGauPffRPfTsS+YpNoxg4eXMPBJprS9va8L2lCBPyUYEW77SSX/H2FHqPjVaxx
/7j39Eu/oYtt5axnFBCYMZ2680kkFycd1RnCqhRJ8KZs6zhv3B8nQp6iS9dXrhg=
=+mjr
-----END PGP SIGNATURE-----
```

**FIGURE 12: A PGP signed message. The sender uses their private key to sign the message; at the destination, the sender's e-mail address yields the public key from the receiver's keyring in order to validate the signature.**

Figure 12 shows a PGP signed message. This message will not be kept secret from an eavesdropper, but a recipient can be assured that the message has not been altered from what the sender transmitted. In this instance, the sender signs the message using their own private key. The receiver uses the sender's public key to verify the signature; the public key is taken from the receiver's keyring based on the sender's e-mail address. Note that the signature process does not work unless the sender's public key is on the receiver's keyring.

```
-----BEGIN PGP MESSAGE-----
Comment: GPGTools - https://gpgtools.org

hQEMA02ePRsA5fMjAQf/fJIFvXKggtfaSAXJzRRZ3sXhKJN+0kH5Kj7GdqbhBd81
n0TZ31BAoXksEQ0Q3HbN8PbJ4qTwLE+glAqVqaGfGmieEirD74/6jX/jffuA028+
X110IX4gJTpkhHzYeabrxPy9yerjI2EQL0XI4313K7w4vKZ8kAEVU86+DCHKm3br
B/UrYlYDPg2xPjgqIx8Zyga2fSnb4TvqYs2+6k9O7RHKu72wKY0H/xx+rqhEmEAk
L/sIxrlCufVM22zEnlbhO5BEqRhBN6CkRS7HkvxOWHetw4dOIbCjc1WI2CMGzHoK
Lx2a3wOskjFbS+0PnDU/CKJV002fO+MCxvYhsF3B7dLAFAG80+08XFBKMll99/Wj
bttk96NLkaz45SG9KNJyqj7KaRFYscnUbEjHunvFIJCnl0CblJb3J/gBaa/ZbSLq
0GGDr9oc0tKoR97mabkyAhohcpiIn7f2y2aCBx5qTRT2uMiedmu4XtyktgB4LUo+
/MspTWlbcyKk9+Gx+9a7QaRkvBskwDn1wL/EIfNhXvtga+qGCV3rZwEX7f46jdzi
NcptK7urvqiWgYtKS1z/0VrppbHBoTux3TQcFAEzKAGCYnS2YCyjNJRqTC/ODPE8
BIIYcYNq
=+eqR
-----END PGP MESSAGE-----
```

**FIGURE 13: A PGP encrypted message. The receiver's e-mail address is the pointer to the public key in the sender's keyring with which to encrypt the message. At the destination side, the receiver uses their own private key to decrypt the message.**

Figure 13 shows a PGP encrypted message (PGP compresses the file, where practical, prior to encryption because encrypted files have a high degree of randomness and, therefore, cannot be efficiently compressed). In this example, public key methods are used to exchange the session key for the actual message encryption that employs secret-key cryptography. In this case, the receiver's e-mail address is the pointer to the public key in the sender's keyring; in fact, the same message can be sent to multiple recipients and the message will not be significantly longer since all that needs to be added is the session key encrypted by each receiver's public key. When the message is received, the recipient will use their private key to extract the session secret key to successfully decrypt the message (Figure 14).

```
Hi Gary,

"Outside of a dog, a book is man's best friend.
Inside of a dog, it's too dark to read."

Carol
```

**FIGURE 14: The decrypted message.**

It is worth noting that PGP was one of the first so-called "hybrid cryptosystems" that combined aspects of SKC and PKC. When Zimmermann was first designing PGP in the late-1980s, he wanted to use RSA to encrypt the entire message. The PCs of the days, however, suffered significant performance degradation when executing RSA so he hit upon the idea of using SKC to encrypt the message and PKC to encrypt the SKC key.

PGP went into a state of flux in 2002. Zimmermann sold PGP to Network Associates, Inc. (NAI) in 1997 and then resigned from NAI in early 2001. In March 2002, NAI announced that they were dropping support for the commercial version of PGP having failed to find a buyer for the product willing to pay what they wanted. In August 2002, PGP was purchased from NAI by PGP Corp. which, in turn, was purchased by [Symantec](http://www.symantec.com/products-solutions/families/?fid=encryption). Meanwhile, there are many freeware versions of PGP available through the [International PGP Page](http://pgpi.didisoft.com/) and the [OpenPGP Alliance](http://openpgp.org/). OpenPGP is described more in [RFC 9580](https://www.rfc-editor.org/info/rfc9580).

The open-source programming GNU (an acronym for "GNU's Not Unix") project has developed GnuPG, aka _GPG_. For additional information, see the [GNU Privacy Guard (GnuPG)](http://www.gnupg.org/) and [GPGTools](https://gpgtools.org/) Web pages.

#### IP Security (IPsec) Protocol

___

> **NOTE:** The information in this section assumes that the reader is familiar with the Internet Protocol (IP), at least to the extent of the packet format and header contents. More information about IP can be found in [_An Overview of TCP/IP Protocols and the Internet_](https://www.garykessler.net/library/tcpip.html). More information about IPv6 can be found in [IPv6: The Next Generation Internet Protocol](https://www.garykessler.net/library/ipv6_exp.html).

___

The Internet and the TCP/IP protocol suite were not built with security in mind. This is not meant as a criticism but as an observation; the baseline IP, TCP, UDP, and ICMP protocols were written in 1980 and built for the relatively closed ARPANET community. TCP/IP wasn't designed for the commercial-grade financial transactions that they now see or for virtual private networks (VPNs) on the Internet. To bring TCP/IP up to today's security necessities, the Internet Engineering Task Force (IETF) formed the [IP Security Protocol Working Group](https://datatracker.ietf.org/wg/ipsec/charter/) which, in turn, developed the IP Security (IPsec) protocol. IPsec is not a single protocol, in fact, but a suite of protocols providing a mechanism to provide data integrity, authentication, privacy, and nonrepudiation for the classic Internet Protocol (IP). Although intended primarily for IP version 6 (IPv6), IPsec can also be employed by the current version of IP, namely IP version 4 (IPv4).

As shown in [Table 3](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#tab03-ipsec), IPsec is described in nearly a dozen RFCs. [RFC 4301](https://www.rfc-editor.org/info/rfc4301), in particular, describes the overall IP security architecture and [RFC 2411](https://www.rfc-editor.org/info/rfc2411) provides an overview of the IPsec protocol suite and the documents describing it.

IPsec can provide either message authentication and/or encryption. The latter requires more processing than the former, but will probably end up being the preferred usage for applications such as VPNs and secure electronic commerce.

Central to IPsec is the concept of a _security association (SA)_. Authentication and confidentiality using AH or ESP use SAs and a primary role of IPsec key exchange it to establish and maintain SAs. An SA is a simplex (one-way or unidirectional) logical connection between two communicating IP endpoints that provides security services to the traffic carried by it using either AH or ESP procedures. The endpoint of an SA can be an IP host or IP security gateway (e.g., a proxy server, VPN server, etc.). Providing security to the more typical scenario of two-way (bi-directional) communication between two endpoints requires the establishment of two SAs (one in each direction).

An SA is uniquely identified by a 3-tuple composed of:

-   Security Parameter Index (SPI), a 32-bit identifier of the connection
-   IP Destination Address
-   security protocol (AH or ESP) identifier

The IP Authentication Header (AH), described in [RFC 4302](https://www.rfc-editor.org/info/rfc4302), provides a mechanism for data integrity and data origin authentication for IP packets using HMAC with MD5 ([RFC 2403](https://www.rfc-editor.org/info/rfc2403) and [RFC 6151](https://www.rfc-editor.org/info/rfc6151)), HMAC with SHA-1 ([RFC 2404](https://www.rfc-editor.org/info/rfc2404)), or HMAC with RIPEMD ([RFC 2857](https://www.rfc-editor.org/info/rfc2857)). See also [RFC 4305](https://www.rfc-editor.org/info/rfc4305).

```
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   | Next Header   |  Payload Len  |          RESERVED             |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                 Security Parameters Index (SPI)               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                    Sequence Number Field                      |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   +                Integrity Check Value-ICV (variable)           |
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

**FIGURE 15: IPsec Authentication Header format. _(From RFC 4302)_**

Figure 15 shows the format of the IPsec AH. The AH is merely an additional header in a packet, more or less representing another protocol layer above IP (this is shown in Figure 17 below). Use of the IP AH is indicated by placing the value 51 (0x33) in the IPv4 Protocol or IPv6 Next Header field in the IP packet header. The AH follows mandatory IPv4/IPv6 header fields and precedes higher layer protocol (e.g., TCP, UDP) information. The contents of the AH are:

-   _Next Header:_ An 8-bit field that identifies the type of the next payload after the Authentication Header.
-   _Payload Length:_ An 8-bit field that indicates the length of AH in 32-bit words (4-byte blocks), minus "2". \[The rationale for this is somewhat counter intuitive but technically important. All IPv6 extension headers encode the header extension length (Hdr Ext Len) field by first subtracting 1 from the header length, which is measured in 64-bit words. Since AH was originally developed for IPv6, it is an IPv6 extension header. Since its length is measured in 32-bit words, however, the Payload Length is calculated by subtracting 2 (32 bit words) to maintain consistency with IPv6 coding rules.\] In the default case, the three 32-bit word fixed portion of the AH is followed by a 96-bit authentication value, so the Payload Length field value would be 4.
-   _Reserved:_ This 16-bit field is reserved for future use and always filled with zeros.
-   _Security Parameters Index (SPI):_ An arbitrary 32-bit value that, in combination with the destination IP address and security protocol, uniquely identifies the Security Association for this datagram. The value 0 is reserved for local, implementation-specific uses and values between 1-255 are reserved by the Internet Assigned Numbers Authority (IANA) for future use.
-   _Sequence Number:_ A 32-bit field containing a sequence number for each datagram; initially set to 0 at the establishment of an SA. AH uses sequence numbers as an anti-replay mechanism, to prevent a "person-in-the-middle" attack. If anti-replay is enabled (the default), the transmitted Sequence Number is never allowed to cycle back to 0; therefore, the sequence number must be reset to 0 by establishing a new SA prior to the transmission of the 2<sup>32</sup>nd packet.
-   _Authentication Data:_ A variable-length, 32-bit aligned field containing the Integrity Check Value (ICV) for this packet (default length = 96 bits). The ICV is computed using the authentication algorithm specified by the SA, such as DES, MD5, or SHA-1. Other algorithms _may_ also be supported.

The IP Encapsulating Security Payload (ESP), described in [RFC 4303](https://www.rfc-editor.org/info/rfc4303), provides message integrity and privacy mechanisms in addition to authentication. As in AH, ESP uses HMAC with MD5, SHA-1, or RIPEMD authentication ([RFC 2403](https://www.rfc-editor.org/info/rfc2403)/[RFC 2404](https://www.rfc-editor.org/info/rfc2404)/[RFC 2857](https://www.rfc-editor.org/info/rfc2857)); privacy is provided using DES-CBC encryption ([RFC 2405](https://www.rfc-editor.org/info/rfc2405)), NULL encryption ([RFC 2410](https://www.rfc-editor.org/info/rfc2410)), other CBC-mode algorithms ([RFC 2451](https://www.rfc-editor.org/info/rfc2451)), or AES ([RFC 3686](https://www.rfc-editor.org/info/rfc3686)). See also [RFC 4305](https://www.rfc-editor.org/info/rfc4305) and [RFC 4308](https://www.rfc-editor.org/info/rfc4308).

```
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ ----
   |               Security Parameters Index (SPI)                 | ^Int.
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ |Cov-
   |                      Sequence Number                          | |ered
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ | ----  
   |                    Payload Data* (variable)                   | |   ^
   ~                                                               ~ |   |
   |                                                               | |Conf.
   +               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ |Cov-
   |               |     Padding (0-255 bytes)                     | |ered*
   +-+-+-+-+-+-+-+-+               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ |   |
   |                               |  Pad Length   | Next Header   | v   v

   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ ------
   |         Integrity Check Value-ICV   (variable)                |
   ~                                                               ~
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

       * If included in the Payload field, cryptographic synchronization
         data, e.g., an Initialization Vector (IV), usually is not
         encrypted per se, although it often is referred to as being
         being part of the ciphertext.
```

**FIGURE 16: IPsec Encapsulating Security Payload format. _(From RFC 4303)_**

Figure 16 shows the format of the IPsec ESP information. Use of the IP ESP format is indicated by placing the value 50 (0x32) in the IPv4 Protocol or IPv6 Next Header field in the IP packet header. The ESP header (i.e., SPI and sequence number) follows mandatory IPv4/IPv6 header fields and precedes higher layer protocol (e.g., TCP, UDP) information. The contents of the ESP packet are:

-   _Security Parameters Index:_ (see description for this field in the AH, above.)
-   _Sequence Number:_ (see description for this field in the AH, above.)
-   _Payload Data:_ A variable-length field containing data as described by the Next Header field. The contents of this field could be encrypted higher layer data or an encrypted IP packet.
-   _Padding:_ Between 0 and 255 octets of padding may be added to the ESP packet. There are several applications that might use the padding field. First, the encryption algorithm that is used may require that the plaintext be a multiple of some number of bytes, such as the block size of a block cipher; in this case, the Padding field is used to fill the plaintext to the size required by the algorithm. Second, padding may be required to ensure that the ESP packet and resulting ciphertext terminate on a 4-byte boundary. Third, padding may be used to conceal the actual length of the payload. Unless another value is specified by the encryption algorithm, the Padding octets take on the value 1, 2, 3, ... starting with the first Padding octet. This scheme is used because, in addition to being simple to implement, it provides some protection against certain forms of "cut and paste" attacks.
-   _Pad Length:_ An 8-bit field indicating the number of bytes in the Padding field; contains a value between 0-255.
-   _Next Header:_ An 8-bit field that identifies the type of data in the Payload Data field, such as an IPv6 extension header or a higher layer protocol identifier.
-   _Authentication Data:_ (see description for this field in the AH, above.)

Two types of SAs are defined in IPsec, regardless of whether AH or ESP is employed. A _transport mode SA_ is a security association between two hosts. Transport mode provides the authentication and/or encryption service to the higher layer protocol. This mode of operation is only supported by IPsec hosts. A _tunnel mode SA_ is a security association applied to an IP tunnel. In this mode, there is an "outer" IP header that specifies the IPsec destination and an "inner" IP header that specifies the destination for the IP packet. This mode of operation is supported by both hosts and security gateways.

```
  <b>ORIGINAL PACKET BEFORE APPLYING AH</b>

         ----------------------------
   <b>IPv4</b>  |orig IP hdr  |     |      |
         |(any options)| TCP | Data |
         ----------------------------

         ---------------------------------------
   <b>IPv6</b>  |             | ext hdrs |     |      |
         | orig IP hdr |if present| TCP | Data |
         ---------------------------------------

  <b>AFTER APPLYING AH (TRANSPORT MODE)</b>

          -------------------------------------------------------
    <b>IPv4</b>  |original IP hdr (any options) | AH | TCP |    Data   |
          -------------------------------------------------------
          |&lt;- mutable field processing -&gt;|&lt;- immutable fields -&gt;|
          |&lt;----- authenticated except for mutable fields -----&gt;|

         ------------------------------------------------------------
   <b>IPv6</b>  |             |hop-by-hop, dest*, |    | dest |     |      |
         |orig IP hdr  |routing, fragment. | AH | opt* | TCP | Data |
         ------------------------------------------------------------
         |&lt;--- mutable field processing --&gt;|&lt;-- immutable fields --&gt;|
         |&lt;---- authenticated except for mutable fields -----------&gt;|

               * = if present, could be before AH, after AH, or both


  <b>AFTER APPLYING AH (TUNNEL MODE)</b>

        ----------------------------------------------------------------
   <b>IPv4</b> |                              |    | orig IP hdr*  |   |      |
        |new IP header * (any options) | AH | (any options) |TCP| Data |
        ----------------------------------------------------------------
        |&lt;- mutable field processing -&gt;|&lt;------ immutable fields -----&gt;|
        |&lt;- authenticated except for mutable fields in the new IP hdr-&gt;|

        --------------------------------------------------------------
   <b>IPv6</b> |           | ext hdrs*|    |            | ext hdrs*|   |    |
        |new IP hdr*|if present| AH |orig IP hdr*|if present|TCP|Data|
        --------------------------------------------------------------
        |&lt;--- mutable field --&gt;|&lt;--------- immutable fields --------&gt;|
        |       processing     |
        |&lt;-- authenticated except for mutable fields in new IP hdr -&gt;|

          * = if present, construction of outer IP hdr/extensions and
              modification of inner IP hdr/extensions is discussed in
              the Security Architecture document.
```

**FIGURE 17: IPsec tunnel and transport modes for AH. _(Adapted from RFC 4302)_**

Figure 17 shows the IPv4 and IPv6 packet formats when using AH in both transport and tunnel modes. Initially, an IPv4 packet contains a normal IPv4 header (which may contain IP options), followed by the higher layer protocol header (e.g., TCP or UDP), followed by the higher layer data itself. An IPv6 packet is similar except that the packet starts with the mandatory IPv6 header followed by any IPv6 extension headers, and then followed by the higher layer data.

Note that in both transport and tunnel modes, the **entire** IP packet is covered by the authentication _except for the mutable fields._ A field is _mutable_ if its value might change during transit in the network; IPv4 mutable fields include the fragment offset, time to live, and checksum fields. Note, in particular, that the address fields are _not_ mutable.

```
    <b>ORIGINAL PACKET BEFORE APPLYING ESP</b>

            ----------------------------
      <b>IPv4</b>  |orig IP hdr  |     |      |
            |(any options)| TCP | Data |
            ----------------------------


            ---------------------------------------
      <b>IPv6</b>  |             | ext hdrs |     |      |
            | orig IP hdr |if present| TCP | Data |
            ---------------------------------------


    <b>AFTER APPLYING ESP (TRANSPORT MODE)</b>


            -------------------------------------------------
      <b>IPv4</b>  |orig IP hdr  | ESP |     |      |   ESP   | ESP|
            |(any options)| Hdr | TCP | Data | Trailer | ICV|

            -------------------------------------------------
                                |&lt;---- encryption ----&gt;|
                          |&lt;-------- integrity -------&gt;|

            ---------------------------------------------------------
      <b>IPv6</b>  | orig |hop-by-hop,dest*,|   |dest|   |    | ESP   | ESP|
            |IP hdr|routing,fragment.|ESP|opt*|TCP|Data|Trailer| ICV|
            ---------------------------------------------------------
                                         |&lt;--- encryption ----&gt;|
                                     |&lt;------ integrity ------&gt;|

                * = if present, could be before ESP, after ESP, or both


    <b>AFTER APPLYING ESP (TUNNEL MODE)</b>

            -----------------------------------------------------------
      <b>IPv4</b>  | new IP hdr+ |     | orig IP hdr+  |   |    | ESP   | ESP|
            |(any options)| ESP | (any options) |TCP|Data|Trailer| ICV|

            -----------------------------------------------------------
                                |&lt;--------- encryption ---------&gt;|
                          |&lt;------------- integrity ------------&gt;|

            ------------------------------------------------------------
      <b>IPv6</b>  | new+ |new ext |   | orig+|orig ext |   |    | ESP   | ESP|

            |IP hdr| hdrs+  |ESP|IP hdr| hdrs+   |TCP|Data|Trailer| ICV|
            ------------------------------------------------------------
                                |&lt;--------- encryption ----------&gt;|
                            |&lt;------------ integrity ------------&gt;|


            + = if present, construction of outer IP hdr/extensions and
                modification of inner IP hdr/extensions is discussed in
                the Security Architecture document.
```

**FIGURE 18: IPsec tunnel and transport modes for ESP. _(Adapted from RFC 4303)_**

Figure 18 shows the IPv4 and IPv6 packet formats when using ESP in both transport and tunnel modes.

-   As with AH, we start with a standard IPv4 or IPv6 packet.
-   In transport mode, the higher layer header and data, as well as ESP trailer information, is encrypted and the entire ESP packet is authenticated. In the case of IPv6, some of the IPv6 extension options can precede or follow the ESP header.
-   In tunnel mode, the original IP packet is encrypted and placed inside of an "outer" IP packet, while the entire ESP packet is authenticated.

Note a significant difference in the scope of ESP and AH. AH authenticates the entire packet transmitted on the network whereas ESP only covers a portion of the packet transmitted on the network (the higher layer data in transport mode and the entire original packet in tunnel mode). The reason for this is straight-forward; in AH, the authentication data for the transmission fits neatly into an additional header whereas ESP creates an entirely new packet which is the one encrypted and/or authenticated. But the ramifications are significant. ESP transport mode as well as AH in both modes protect the IP address fields of the original transmissions. Thus, using IPsec in conjunction with network address translation (NAT) _might_ be problematic because NAT changes the values of these fields _after_ IPsec processing.

The third component of IPsec is the establishment of security associations and key management. These tasks can be accomplished in one of two ways.

The simplest form of SA and key management is manual management. In this method, a security administer or other individual manually configures each system with the key and SA management data necessary for secure communication with other systems. Manual techniques are practical for small, reasonably static environments but they do not scale well.

For successful deployment of IPsec, however, a scalable, automated SA/key management scheme is necessary. Several protocols have defined for these functions:

-   The Internet Security Association and Key Management Protocol (ISAKMP) defines procedures and packet formats to establish, negotiate, modify and delete security associations, and provides the framework for exchanging information about authentication and key management ([RFC 2407](https://www.rfc-editor.org/info/rfc2407)/[RFC 2408](https://www.rfc-editor.org/info/rfc2408)). ISAKMP's security association and key management is totally separate from key exchange.
-   The OAKLEY Key Determination Protocol ([RFC 2412](https://www.rfc-editor.org/info/rfc2412)) describes a scheme by which two authenticated parties can exchange key information. OAKLEY uses the Diffie-Hellman key exchange algorithm.
-   The Internet Key Exchange (IKE) algorithm ([RFC 2409](https://www.rfc-editor.org/info/rfc2409)) is the default automated key management protocol for IPsec.
-   An alternative to IKE is Photuris ([RFC 2522](https://www.rfc-editor.org/info/rfc2522)/[RFC 2523](https://www.rfc-editor.org/info/rfc2523)), a scheme for establishing short-lived session-keys between two authenticated parties without passing the session-keys across the Internet. IKE typically creates keys that may have very long lifetimes.

#### Secure Transactions with SSL and TLS

The Secure Sockets Layer (SSL) protocol was developed by Netscape Communications to provide application-independent secure communication over the Internet for protocols such as the Hypertext Transfer Protocol (HTTP). SSL employs RSA and X.509 certificates during an initial handshake used to authenticate the server (client authentication is optional). The client and server then agree upon an encryption scheme. SSL v2.0 (1995), the first version publicly released, supported RC2 and RC4 with 40-bit keys. SSL v3.0 (1996) added support for DES, RC4 with a 128-bit key, and 3DES with a 168-bit key, all along with either MD5 or SHA-1 message hashes; this protocol is described in [RFC 6101](https://www.rfc-editor.org/info/rfc6101).

![](https://www.garykessler.net/library/images/crypto_sslv3.png)

**FIGURE 19: Browser encryption configuration screen (Firefox).**

In 1997, SSL v3 was found to be breakable. By this time, the Internet Engineering Task Force (IETF) had already started work on a new, non-proprietary protocol called Transport Layer Security (TLS), described in [RFC 2246](https://www.rfc-editor.org/info/rfc2246) (1999). TLS extends SSL and supports additional crypto schemes, such as Diffie-Hellman key exchange and DSS digital signatures; [RFC 4279](https://www.rfc-editor.org/info/rfc4279) describes the pre-shared key crypto schemes supported by TLS. TLS is backward compatible with SSL (and, in fact, is recognized as SSL v3.1). SSL v3.0 and TLS v1.0 are the commonly supported versions on servers and browsers today (Figure 19); SSL v2.0 is rarely found today and, in fact, [RFC 6176](https://www.rfc-editor.org/info/rfc6176)\-compliant clients and servers that support TLS will never negotiate the use of SSL v2.

In 2002, a cipher block chaining (CBC) vulnerability was described for TLS v1.0. In 2011, the theoretical became practical when a CBC proof-of-concept exploit was released. Meanwhile, TLS v1.1 was defined in 2006 ([RFC 4346](https://www.rfc-editor.org/info/rfc4346)), adding protection against v1.0's CBC vulnerability. In 2008, TLS v1.2 was defined ([RFC 5246](https://www.rfc-editor.org/info/rfc5246)), adding several additional cryptographic options. In 2018, TLS v1.3 was introduced ([RFC 8446](https://www.rfc-editor.org/info/rfc8446)), adding clarification of acceptable crypto methods and better security.

```
       Client                                           Server

Key  ^ ClientHello
Exch | + key_share*
     | + signature_algorithms*
     | + psk_key_exchange_modes*
     v + pre_shared_key*       --------&gt;
                                                  ServerHello  ^ Key
                                                 + key_share*  | Exch
                                            + pre_shared_key*  v

                                        {EncryptedExtensions}  ^  Server
                                        {CertificateRequest*}  v  Params

                                               {Certificate*}  ^
                                         {CertificateVerify*}  | Auth
                                                   {Finished}  v

                               &lt;--------  [Application Data*]

     ^ {Certificate*}
Auth | {CertificateVerify*}
     v {Finished}              --------&gt;

       [Application Data]      &lt;-------&gt;  [Application Data]

 
+  Indicates noteworthy extensions sent in the previously noted message.
*  Indicates optional or situation-dependent messages/extensions that are not always sent.
{} Indicates messages protected using keys derived from a [sender]_handshake_traffic_secret.
[] Indicates messages protected using keys derived from [sender]_application_traffic_secret_N.
```

**FIGURE 20: Basic TLS protocol handshake. _(From RFC 8446)_**

The use of TLS is initiated when a user specifies the protocol _https://_ in a URL. The client (i.e., browser) will automatically try to make a TCP connection to the server at port 443 (unless another port is specified in the URL). The communication between the client and server comprises the TLS protocol handshake (Figure 20), which has three phases, followed by actual data exchange.

The first phase of the protocol handshake is _Key Exchange_, used to establish the shared key and select the encryption method. This is the only phase of TLS communication that is not encrypted. During this phase:

-   The client sends a _ClientHello_ message, composed of a random nonce, encryption protocols and versions that it can employ, and other parameters (and possible message extensions) required to negotiate the encryption protocol. (**NOTE:** One form of attack is to specify only old versions of protocols with known vulnerabilities and exploits. This is why servers are best advised to limit backward and downward compatibility.)
-   The server responds with a _ServerHello_ message, indicating the negotiated encryption and other connection parameters. The combination of these two "Hello" messages determines the shared encryption keys.

From this point forward, all communication is encrypted. The second step of the protocol handshake is the _Server Parameters_ phase, where the server specifies other, additional handshake parameters. The server accomplishes this task by the use of two messages:

-   The _EncryptedExtensions_ message responds to any extensions that were included in the _ClientHello_ message that were not required to determine the cryptographic parameters.
-   A _CertificateRequest_ message is only used if certificate-based client authentication is employed, in which case this message contains the desired parameters for that certificate.

The third, and final phase, of the TLS protocol handshake is _Authentication_, during which the server is authenticated (and, optionally, the client), keys are confirmed, and the integrity of the handshake assured. The messages exchanged during this phase include:

-   A _Certificate_ message contains the X.509 certificate of the sender (i.e., the client or the server). The server will not send this message if it is not authenticating with a certificate; the client will only send a certificate if the server sent a _CertificateRequest_ during the Server Parameters phase of the protocol handshake.
-   The _CertificateVerify_ message contains a digital signature covering the entire protocol handshake exchange, employing the private key associated to the public key in the previously sent _Certificate_ message. As above, this message is only sent by the client or server if they are employing certificate-based authentication.
-   The _Finished_ message contains a Message Authentication Code (MAC) over the entire handshake.

During this phase, the server sends its authentication messages followed by the client sending its authentication messages. Once the _Finished_ messages have been exchanged, the protocol handshake is complete, and the client and server now start to exchange encrypted _Application Data_.

**Side Note:** It would probably be helpful to make some mention of how SSL/TLS is most commonly used today. Most of us have used SSL to engage in a secure, private transaction with some vendor. The steps are something like this. During the SSL exchange with the vendor's secure server, the server sends its certificate to our client software. The certificate includes the vendor's public key and a validation of some sort from the CA that issued the vendor's certificate (signed with the CA's private key). Our browser software is shipped with the major CAs' certificates containing their public keys; in that way, the client software can authenticate the server's certificate. Note that the server generally does _not_ use a certificate to authenticate the client. Instead, purchasers are generally authenticated when a credit card number is provided; the server checks to see if the card purchase will be authorized by the credit card company and, if so, considers us valid and authenticated!

The reason that only the server is authenticated is rooted in history. SSL was developed to support e-commerce by providing a _trust mechanism_ so that customers could have faith in a merchant. In the real world, you "trust" a store because you can walk into a brick-and-mortar structure. The store doesn't know who the customer is; they check to see if the credit card is valid and, if so, a purchase goes through. In e-commerce, we were trying to replicate the same experience — find a way in which the customer could trust the store and then make sure that the credit card was valid. In addition, how many people would have been willing to purchase an individual certificate and install it on their browser merely so that they shop online? This latter requirement, if implemented, could have killed e-commerce before it ever got started.

This all said, bidirectional — or mutual — authentication is supported by SSL, as noted in the figure above. See E. Cheng's ["An Introduction to Mutual SSL Authentication"](https://www.codeproject.com/Articles/326574/An-Introduction-to-Mutual-SSL-Authentication) for an overview of how symmetric the process can be.

For historical purposes, it is worth mentioning Microsoft's [Server Gated Cryptography (SGC)](https://en.wikipedia.org/wiki/Server-Gated_Cryptography) protocol, another (now long defunct) extension to SSL/TLS. For several decades, it had been illegal to generally export products from the U.S. that employed secret-key cryptography with keys longer than 40 bits. For that reason, during the 1996-1998 time period, browsers using SSL/TLS (e.g., Internet Explorer and Netscape Navigator) had an exportable version with weak (40-bit) keys and a domestic (North American) version with strong (128-bit) keys. By the late-1990s, products using strong SKC has been approved for the worldwide financial community. SGC was an extension to SSL that allowed financial institutions using Windows NT servers to employ strong cryptography. Both the client and server needed to have implemented SGC and the bank had to have a valid SGC certificate. During the initial handshake, the server would indicate support of SGC and supply its SGC certificate; if the client wished to use SGC and validated the server's SGC certificate, it would establish a secure session employing 128-bit RC2, 128-bit RC4, 56-bit DES, or 168-bit 3DES encryption. Microsoft supported SGC in the Windows 95/98/NT versions of Internet Explorer 4.0, Internet Information Server (IIS) 4.0, and Money 98.

As mentioned earlier, SSL was designed to provide application-independent transaction security for the Internet. Although the discussion above has focused on HTTP over SSL (https/TCP port 443), SSL can be used with several TCP/IP protocols (Table 4).

<table><caption><b>TABLE 4. Internet protocols over SSL.</b></caption><tbody><tr><th>Protocol</th><th>TCP Port Name/Number</th></tr><tr><td>File Transfer Protocol (FTP)</td><td>ftps-data/989 &amp; ftps/990</td></tr><tr><td>Hypertext Transfer Protocol (FTP)</td><td>https/443</td></tr><tr><td>Internet Message Access Protocol v4 (IMAP4)</td><td>imaps/993</td></tr><tr><td>Lightweight Directory Access Protocol (LDAP)</td><td>ldaps/636</td></tr><tr><td>Network News Transport Protocol (NNTP)</td><td>nntps/563</td></tr><tr><td>Post Office Protocol v3 (POP3)</td><td>pop3s/995</td></tr><tr><td>Telnet</td><td>telnets/992</td></tr></tbody></table>

TLS was originally designed to operate over TCP. The IETF developed the Datagram Transport Layer Security (DTLS) protocol, based upon TLS, to operate over UDP. DTLS v1.3 is described in [RFC 9147](https://www.rfc-editor.org/info/rfc9147). ([RFC 6347](https://www.rfc-editor.org/info/rfc6347) defines DTLS v1.2 and DTLS v1.0 can be found in [RFC 4347](https://www.rfc-editor.org/info/rfc4347).) [RFC 6655](https://www.rfc-editor.org/info/rfc6655) describes a suite of AES in Counter with Cipher Block Chaining - Message Authentication Code (CBC-MAC) Mode (CCM) ciphers for use with TLS and DTLS. An interesting analysis of the TLS protocol can be found in the paper "[Analysis and Processing of Cryptographic Protocols](http://www.cs.ru.ac.za/research/g06c5476/Honours/CryptoProtos2009Cowie.pdf)" by Cowie.

![](https://www.garykessler.net/library/images/crypto_heartbleed.png)

**Vulnerabilities:** A vulnerability in the OpenSSL Library was discovered in 2014. Known as [Heartbleed](http://heartbleed.com/), this vulnerability had apparently been introduced into OpenSSL in late 2011 with the introduction of a feature called _heartbeat_. Heartbleed exploited an implementation flaw in order to exfiltrate keying material from an SSL server (or some SSL clients, in what is known at _reverse Heartbleed_); the flaw allowed an attacker to grab 64 KB blocks from RAM. Heartbleed is known to only affect OpenSSL v1.0.1 through v1.0.1f; the exploit was patched in v1.0.1g. In addition, the OpenSSL 0.9.8 and 1.0.0 families are not vulnerable. Note also that [Heartbleed affects some versions of the Android operating system](http://www.slideshare.net/LookoutInc/heartbleed-android), notably v4.1.0 and v4.1.1 (and some, possibly custom, implementations of v4.2.2). _Note that Heartbleed did **not** exploit a flaw in the SSL protocol, but rather a flaw in the OpenSSL implementation._

But that wasn't the only problem with SSL. In October 2014, a new vulnerability was found called [POODLE](https://en.wikipedia.org/wiki/POODLE) (Padding Oracle On Downgraded Legacy Encryption), a man-in-the-middle attack that exploited another SSL vulnerability that had unknowingly been in place for many years. Weeks later, an SSL vulnerability in the bash Unix command shell was discovered, aptly named [Shellshock](http://www.symantec.com/connect/blogs/shellshock-all-you-need-know-about-bash-bug-vulnerability). (Here's a nice overview of the [2014 SSL](https://blogs.akamai.com/2014/10/poodle-shellshock-and-heartbleed-resources.html) problems!) In March 2015, the [Bar Mitzvah Attack](http://securityaffairs.co/wordpress/35352/hacking/bar-mitzvah-attack-on-rc4.html) was exposed, exploiting a 13-year old vulnerability in the Rivest Cipher 4 (RC4) encryption algorithm. Then there was the [FREAK (Factoring Attack on RSA-EXPORT Keys CVE-2015-0204)](https://www.us-cert.gov/ncas/current-activity/2015/03/06/FREAK-SSLTLS-Vulnerability) SSL/TLS Vulnerability that affected some SSL/TLS implementations, including Android OS and Chrome browser for OS X later that month.

In March 2016, the SSL [DROWN](https://drownattack.com/) (Decrypting RSA with Obsolete and Weakened eNcryption) attack was announced. DROWN works by exploiting the presence of SSLv2 to crack encrypted communications and steal information from Web servers, email servers, or VPN sessions. You might have read above that SSLv2 fell out of use by the early 2000s and was formally deprecated in 2011. This is true. But backward compatibility often causes old software to remain dormant and it seems that up to one-third of all HTTPS sites at the time were vulnerable to DROWN because SSLv2 had not been removed or disabled.

#### Elliptic Curve Cryptography (ECC)

In general, public key cryptography systems use hard-to-solve problems as the basis of the algorithm. The most predominant public key cryptography algorithm for many years was RSA, based on the prime factors of very large integers. While RSA can be successfully attacked, the mathematics of the algorithm have not been compromised, per se; instead, computational brute-force has broken the keys. The defense is "simple" — keep the size of the integer to be factored ahead of the computational curve!

In 1985, Elliptic Curve Cryptography (ECC) was proposed independently by cryptographers Victor Miller (IBM) and Neal Koblitz (University of Washington). ECC is based on the difficulty of solving the Elliptic Curve Discrete Logarithm Problem (ECDLP). Like the prime factorization problem, ECDLP is another "hard" problem that is deceptively simple to state: Given two points, P and Q, on an elliptic curve, find the integer _n_, if it exists, such that Q = nP.

The first thing to note about elliptic curves is that they are neither _elliptic_ (i.e., they are not an oval or ellipse) nor are they _curves_ (i.e., they are not curves in common meaning, which are merely bent lines). Elliptic curves are a part of number theory and algebraic geometry, and can be defined over any field of numbers (i.e., real, integer, complex, etc.). In cryptography, we normally use elliptic curves over a finite field of prime numbers, which we denote F<sub>P</sub>. This is one reason that we use the modulo function; modulo arithmetic defines a finite range of numbers (e.g., 0 to N-1 if using modulo N) which will, in turn, yield a finite number of primes.

So, given this preamble, an elliptic curve in F<sub>P</sub> consists of the set of real numbers (x,y) that satisfies the equation:

y<sup>2</sup> = (x<sup>3</sup> + ax + b) modulo N

In addition, the curve has to be non-singular (sometimes stated as "not containing any singularities"), which means that the curve has no cusps, self-intersections, or isolated points. To ensure this, there is one more condition, namely, the values of a and b must satisfy the requirement that:

4a<sup>3</sup> + 27b<sup>2</sup> ≠ 0

The set of all of the solutions to the equation forms the elliptic curve. Changing _a_ and _b_ changes the shape of the curve, and small changes in these parameters can result in major changes in the set of (x,y) solutions. The ECC standards documents list a host of ECC curves and they differ by changing these parameters.

![](https://www.garykessler.net/library/images/crypto_ecc.png)

**FIGURE 21: Elliptic curve addition. _(Source: Dams, 2012)_**

Figure 21 shows the addition of two points on the elliptic curve where a = -3 and b = 5. Elliptic curves have the interesting property that adding two points on the elliptic curve yields a third point on the curve. Therefore, adding two points, P and Q, gets us to point R. Small changes in P or Q can cause a large change in the position of R.

Now, the astute reader will notice that the line above went through a point labelled -R and that, in the Cartesian coordinate system, we've merely changed the sign of the y coordinate to get the point labelled R; i.e., we've "reflected" the point around the x-axis. It turns out that it doesn't matter because the curve is solving the equation for y<sup>2</sup>, meaning that if a point exists in the +y space, the same point exists in the -y space because (+y)<sup>2</sup> = (-y)<sup>2</sup>. Stated another, slightly more rigorous way, the curve is symmetric about the x-axis.

So, if it doesn't matter, why do it? Remember that our original problem was to find Q = nP. So, to get there, we need to define a number of operations on P, including doubling P and multiplying P by some number. At this point, we leave the area of simple scalar arithmetic and enter into group law and algebraic geometry. There are a bunch of good explanations about why we do this reflection that I am not going to replicate but I will give one key thing to keep in mind. While the sign doesn't matter when squaring a number, it does matter in other types of arithmetic. When adding points on the elliptic curve, we need to maintain the associative law, which says:

(A + B) + C = A + (B + C)

Working with elliptic curves gets us into group laws and the operations often reflect about the x-axis in order to maintain the associative principle. In any case, there's a pretty good discussion about this on [StackExchange](https://crypto.stackexchange.com/questions/53974/when-adding-two-points-on-an-elliptic-curve-why-flip-over-the-x-axis) and in ["The group law"](https://en.wikipedia.org/wiki/Elliptic_curve#The_group_law) section on the ECC Wikipedia page.

So let's go back to the original problem statement from above. The point Q is calculated as a multiple of the starting point, P, _or_, Q = nP. An attacker might know P and Q but finding the integer, _n_, is a difficult problem to solve. Q (i.e., nP) is the public key and _n_ is the private key.

ECC may be employed with many Internet standards, including CCITT X.509 certificates and certificate revocation lists (CRLs), Internet Key Exchange (IKE), Transport Layer Security (TLS), XML signatures, and applications or protocols based on the cryptographic message syntax (CMS). ANSI, IEEE, IETF, NIST, the Standards for Efficient Cryptography Group (SECG), and others have also defined "safe curves" for use in various applications for ECC, such as:

-   [RFC 5639](https://www.rfc-editor.org/info/rfc5639): Elliptic Curve Cryptography (ECC) Brainpool Standard Curves and Curve Generation (2010)
-   [RFC 6637](https://www.rfc-editor.org/info/rfc6637): Elliptic Curve Cryptography (ECC) in OpenPGP (2012) proposes additional elliptic curves for use with OpenPGP (e.g., NIST's [FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf): Digital Signature Standard (DSS) \[NIST, 2013\]
-   [SEC 2](http://www.secg.org/sec2-v2.pdf): Recommended Elliptic Curve Domain Parameters (SECG, 2010)

See Bernstein and Lange's [SafeCurves: choosing safe curves for elliptic-curve cryptography](https://safecurves.cr.yp.to/) site for a review and analysis of various ECC curve standard specifications.

<table><caption><b>TABLE 5. ECC and RSA Key Comparison, and Equivalent AES Key Size. <i>(Source: Certicom, NIST)</i></b></caption><tbody><tr><th>ECC Key Size</th><th>RSA Key Size</th><th>Key-Size<br>Ratio</th><th>AES Key Size</th></tr><tr><td>160</td><td>1,024</td><td>1:6</td><td>n/a</td></tr><tr><td>256</td><td>3,072</td><td>1:12</td><td>128</td></tr><tr><td>384</td><td>7,680</td><td>1:20</td><td>192</td></tr><tr><td>512</td><td>15,360</td><td>1:30</td><td>256</td></tr><tr><td colspan="4"><span size="-1">Key sizes in bits.</span></td></tr></tbody></table>

RSA had been the mainstay of PKC since its development in the late 1970s. ECC has emerged as a replacement in many environments because it provides similar levels of security compared to RSA but with significantly reduced key sizes and, therefore, reduced processing demands. Table 5 shows the key size relationship between ECC and RSA, and the appropriate choice of AES key size.

Since the ECC key sizes are so much shorter than comparable RSA keys, the length of the public key and private key is much shorter in elliptic curve cryptosystems. This results into faster processing times, and lower demands on memory and bandwidth; some studies have found that ECC is faster than RSA for signing and decryption, but slower for signature verification and encryption. ECC is particularly useful in applications where memory, bandwidth, and/or computational power is limited (e.g., a smartcard or smart device) and it is in this area that ECC use has been growing.

There are a number of exceptional ECC tutorials online at various levels of detail and complexity to which readers are referred:

-   [Elliptic Curve Cryptography](https://www.johannes-bauer.com/compsci/ecc/) (Bauer)
-   ["ECCHacks: a gentle introduction to elliptic-curve cryptography"](https://fahrplan.events.ccc.de/congress/2014/Fahrplan/system/attachments/2501/original/20141227.pdf) (Bernstein & Lange)
-   [ECC Tutorial](https://www.certicom.com/content/certicom/en/ecc-tutorial.html) (Certicom)
-   [An Introduction to Elliptic Curve Cryptography](https://www.purplealienplanet.com/node/27) (Dams) \[See also [embedded.com](https://www.embedded.com/an-introduction-to-elliptic-curve-cryptography/)\]
-   [A (Relatively Easy To Understand) Primer on Elliptic Curve Cryptography](https://blog.cloudflare.com/a-relatively-easy-to-understand-primer-on-elliptic-curve-cryptography/) (Sullivan)
-   [Elliptic-curve cryptography](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography) (Wikipedia)

#### The Advanced Encryption Standard (AES) and Rijndael

The search for a replacement to DES started in January 1997 when NIST announced that it was looking for an Advanced Encryption Standard. In September of that year, they put out a formal Call for Algorithms and in August 1998 announced that 15 candidate algorithms were being considered (Round 1). In April 1999, NIST announced that the 15 had been whittled down to five finalists (Round 2): _[MARS](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.35.6084&rep=rep1&type=pdf)_ (multiplication, addition, rotation and substitution) from IBM; Ronald Rivest's _[RC6](https://people.csail.mit.edu/rivest/pubs/RRSY98.pdf)_; _[Rijndael](http://csrc.nist.gov/archive/aes/rijndael/Rijndael-ammended.pdf)_ from a Belgian team; _[Serpent](http://www.cl.cam.ac.uk/~rja14/serpent.html)_, developed jointly by a team from England, Israel, and Norway; and _[Twofish](https://www.schneier.com/cryptography/twofish/)_, developed by Bruce Schneier. In October 2000, NIST announced their selection: Rijndael.

The remarkable thing about this entire process has been the openness as well as the international nature of the "competition." NIST maintained an excellent Web site devoted to keeping the public fully informed, at _[http://csrc.nist.gov/archive/aes/](http://csrc.nist.gov/archive/aes/)_, which is now available as an archive site. Their [Overview of the AES Development Effort](http://csrc.nist.gov/archive/aes/index2.html) has full details of the process, algorithms, and comments so I will not repeat everything here.

In October 2000, NIST released the [Report on the Development of the Advanced Encryption Standard (AES)](http://csrc.nist.gov/archive/aes/round2/r2report.pdf) that compared the five Round 2 algorithms in a number of categories. Table 6 summarizes the relative scores of the five schemes (1=low, 3=high):

<table><caption><b>TABLE 6. Scores of AES Round 2 algorithms.</b></caption><tbody><tr><th></th><th colspan="5">Algorithm</th></tr><tr><th>Category</th><th>MARS</th><th>RC6</th><th>Rijndael</th><th>Serpent</th><th>Twofish</th></tr><tr><th>General security</th><td>3</td><td>2</td><td>2</td><td>3</td><td>3</td></tr><tr><th>Implementation of security</th><td>1</td><td>1</td><td>3</td><td>3</td><td>2</td></tr><tr><th>Software performance</th><td>2</td><td>2</td><td>3</td><td>1</td><td>1</td></tr><tr><th>Smart card performance</th><td>1</td><td>1</td><td>3</td><td>3</td><td>2</td></tr><tr><th>Hardware performance</th><td>1</td><td>2</td><td>3</td><td>3</td><td>2</td></tr><tr><th>Design features</th><td>2</td><td>1</td><td>2</td><td>1</td><td>3</td></tr></tbody></table>

With the report came the recommendation that Rijndael be named as the AES standard. In February 2001, NIST released the Draft Federal Information Processing Standard (FIPS) AES Specification for public review and comment. AES contains a subset of Rijndael's capabilities (e.g., AES only supports a 128-bit block size) and uses some slightly different nomenclature and terminology, but to understand one is to understand both. The 90-day comment period ended on May 29, 2001 and the U.S. Department of Commerce officially adopted AES in December 2001, published as [FIPS PUB 197](http://csrc.nist.gov/publications/fips/fips197/fips-197.pdf).

**AES (Rijndael) Overview**

Rijndael (pronounced as in "rain doll" or "rhine dahl") is a block cipher designed by Joan Daemen and Vincent Rijmen, both cryptographers in Belgium. Rijndael can operate over a variable-length block using variable-length keys; the [specification submitted to NIST](http://csrc.nist.gov/archive/aes/rijndael/Rijndael-ammended.pdf) describes use of a 128-, 192-, or 256-bit key to encrypt data blocks that are 128, 192, or 256 bits long; note that all nine combinations of key length and block length are possible. The algorithm is written in such a way that block length and/or key length can easily be extended in multiples of 32 bits and it is specifically designed for efficient implementation in hardware or software on a range of processors. The design of Rijndael was strongly influenced by the block cipher called [_Square_](https://en.wikipedia.org/wiki/Square_%28cipher%29), also designed by Daemen and Rijmen.

Rijndael is an iterated block cipher, meaning that the initial input block and cipher key undergoes multiple rounds of transformation before producing the output. Each intermediate cipher result is called a _State_.

For ease of description, the block and cipher key are often represented as an array of columns where each array has 4 rows and each column represents a single byte (8 bits). The number of columns in an array representing the state or cipher key, then, can be calculated as the block or key length divided by 32 (32 bits = 4 bytes). An array representing a State will have **Nb** columns, where **Nb** values of 4, 6, and 8 correspond to a 128-, 192-, and 256-bit block, respectively. Similarly, an array representing a Cipher Key will have **Nk** columns, where **Nk** values of 4, 6, and 8 correspond to a 128-, 192-, and 256-bit key, respectively. An example of a 128-bit State (**Nb**\=4) and 192-bit Cipher Key (**Nk**\=6) is shown below:

<table><tbody><tr><td><table><tbody><tr><td>s<sub>0,0</sub></td><td>s<sub>0,1</sub></td><td>s<sub>0,2</sub></td><td>s<sub>0,3</sub></td></tr><tr><td>s<sub>1,0</sub></td><td>s<sub>1,1</sub></td><td>s<sub>1,2</sub></td><td>s<sub>1,3</sub></td></tr><tr><td>s<sub>2,0</sub></td><td>s<sub>2,1</sub></td><td>s<sub>2,2</sub></td><td>s<sub>2,3</sub></td></tr><tr><td>s<sub>3,0</sub></td><td>s<sub>3,1</sub></td><td>s<sub>3,2</sub></td><td>s<sub>3,3</sub></td></tr></tbody></table></td><td>&nbsp;</td><td><table><tbody><tr><td>k<sub>0,0</sub></td><td>k<sub>0,1</sub></td><td>k<sub>0,2</sub></td><td>k<sub>0,3</sub></td><td>k<sub>0,4</sub></td><td>k<sub>0,5</sub></td></tr><tr><td>k<sub>1,0</sub></td><td>k<sub>1,1</sub></td><td>k<sub>1,2</sub></td><td>k<sub>1,3</sub></td><td>k<sub>1,4</sub></td><td>k<sub>1,5</sub></td></tr><tr><td>k<sub>2,0</sub></td><td>k<sub>2,1</sub></td><td>k<sub>2,2</sub></td><td>k<sub>2,3</sub></td><td>k<sub>2,4</sub></td><td>k<sub>2,5</sub></td></tr><tr><td>k<sub>3,0</sub></td><td>k<sub>3,1</sub></td><td>k<sub>3,2</sub></td><td>k<sub>3,3</sub></td><td>k<sub>3,4</sub></td><td>k<sub>3,5</sub></td></tr></tbody></table></td></tr></tbody></table>

The number of transformation rounds (**Nr**) in Rijndael is a function of the block length and key length, and is shown in Table 7.

<table><caption><b>TABLE 7. Number of transformation rounds in Rijndael.</b></caption><tbody><tr><th colspan="2" rowspan="2">No. of Rounds<br>Nr</th><th colspan="3">Block Size</th></tr><tr><th>128 bits<br>Nb = 4</th><th>192 bits<br>Nb = 6</th><th>256 bits<br>Nb = 8</th></tr><tr><th rowspan="3">Key<br>Size</th><th>128 bits<br>Nk = 4</th><td>10</td><td>12</td><td>14</td></tr><tr><th>192 bits<br>Nk = 6</th><td>12</td><td>12</td><td>14</td></tr><tr><th>256 bits<br>Nk = 8</th><td>14</td><td>14</td><td>14</td></tr></tbody></table>

Now, having said all of this, the AES version of Rijndael does not support all nine combinations of block and key lengths, but only the subset using a 128-bit block size. NIST calls these supported variants AES-128, AES-192, and AES-256 where the number refers to the key size. Table 8 shows the **Nb**, **Nk**, and **Nr** values supported in AES.

<table><caption><b>TABLE 8. Rijndael parameters supported in AES.</b></caption><tbody><tr><th></th><th colspan="3">Parameters</th></tr><tr><th>Variant</th><th>Nb</th><th>Nk</th><th>Nr</th></tr><tr><th>AES-128</th><td>4</td><td>4</td><td>10</td></tr><tr><th>AES-192</th><td>4</td><td>6</td><td>12</td></tr><tr><th>AES-256</th><td>4</td><td>8</td><td>14</td></tr></tbody></table>

The AES/Rijndael cipher itself has three operational stages:

-   AddRound Key transformation
-   **Nr**\-1 Rounds comprising:

-   SubBytes transformation
-   ShiftRows transformation
-   MixColumns transformation
-   AddRoundKey transformation

-   A final Round comprising:

-   SubBytes transformation
-   ShiftRows transformation
-   AddRoundKey transformation

The paragraphs below will describe the operations mentioned above. The nomenclature used below is taken from the AES specification although references to the Rijndael specification are made for completeness. The arrays _s_ and _s'_ refer to the State before and after a transformation, respectively (**NOTE:** The Rijndael specification uses the array nomenclature a and b to refer to the before and after States, respectively). The subscripts _i_ and _j_ are used to indicate byte locations within the State (or Cipher Key) array.

**The SubBytes transformation**

The substitute bytes (called _ByteSub_ in Rijndael) transformation operates on each of the State bytes independently and changes the byte value. An S-box, or _substitution table_, controls the transformation. The characteristics of the S-box transformation as well as a compliant S-box table are provided in the AES specification; as an example, an input State byte value of 107 (0x6b) will be replaced with a 127 (0x7f) in the output State and an input value of 8 (0x08) would be replaced with a 48 (0x30).

One way to think of the SubBytes transformation is that a given byte in State s is given a new value in State s' according to the S-box. The S-box, then, is a function on a byte in State s so that:

s'<sub>i,j</sub> = S-box (s<sub>i,j</sub>)

The more general depiction of this transformation is shown by:

<table><tbody><tr><td><table><tbody><tr><td>s<sub>0,0</sub></td><td>s<sub>0,1</sub></td><td>s<sub>0,2</sub></td><td>s<sub>0,3</sub></td></tr><tr><td>s<sub>1,0</sub></td><td>s<sub>1,1</sub></td><td>s<sub>1,2</sub></td><td>s<sub>1,3</sub></td></tr><tr><td>s<sub>2,0</sub></td><td>s<sub>2,1</sub></td><td>s<sub>2,2</sub></td><td>s<sub>2,3</sub></td></tr><tr><td>s<sub>3,0</sub></td><td>s<sub>3,1</sub></td><td>s<sub>3,2</sub></td><td>s<sub>3,3</sub></td></tr></tbody></table></td><td></td><td></td><td></td><td><table><tbody><tr><td>s'<sub>0,0</sub></td><td>s'<sub>0,1</sub></td><td>s'<sub>0,2</sub></td><td>s'<sub>0,3</sub></td></tr><tr><td>s'<sub>1,0</sub></td><td>s'<sub>1,1</sub></td><td>s'<sub>1,2</sub></td><td>s'<sub>1,3</sub></td></tr><tr><td>s'<sub>2,0</sub></td><td>s'<sub>2,1</sub></td><td>s'<sub>2,2</sub></td><td>s'<sub>2,3</sub></td></tr><tr><td>s'<sub>3,0</sub></td><td>s'<sub>3,1</sub></td><td>s'<sub>3,2</sub></td><td>s'<sub>3,3</sub></td></tr></tbody></table></td></tr></tbody></table>

**The ShiftRows transformation**

The shift rows (called _ShiftRow_ in Rijndael) transformation cyclically shifts the bytes in the bottom three rows of the State array. According to the more general Rijndael specification, rows 2, 3, and 4 are cyclically left-shifted by C1, C2, and C3 bytes, respectively, as shown below:

<table><tbody><tr><th>Nb</th><td>C1</td><td>C2</td><td>C3</td></tr><tr><td>4</td><td>1</td><td>2</td><td>3</td></tr><tr><td>6</td><td>1</td><td>2</td><td>3</td></tr><tr><td>8</td><td>1</td><td>3</td><td>4</td></tr></tbody></table>

The current version of AES, of course, only allows a block size of 128 bits (**Nb** = 4) so that C1=1, C2=2, and C3=3. The diagram below shows the effect of the ShiftRows transformation on State s:

<table><tbody><tr><td><table><caption>State s</caption><tbody><tr><td>s<sub>0,0</sub></td><td>s<sub>0,1</sub></td><td>s<sub>0,2</sub></td><td>s<sub>0,3</sub></td></tr><tr><td>s<sub>1,0</sub></td><td>s<sub>1,1</sub></td><td>s<sub>1,2</sub></td><td>s<sub>1,3</sub></td></tr><tr><td>s<sub>2,0</sub></td><td>s<sub>2,1</sub></td><td>s<sub>2,2</sub></td><td>s<sub>2,3</sub></td></tr><tr><td>s<sub>3,0</sub></td><td>s<sub>3,1</sub></td><td>s<sub>3,2</sub></td><td>s<sub>3,3</sub></td></tr></tbody></table></td><td><table><caption>&nbsp;</caption><tbody><tr><td>------------ no shift -----------&gt;<sub>&nbsp;</sub></td></tr><tr><td>----- left-shift by C1 (1) ----&gt;<sub>&nbsp;</sub></td></tr><tr><td>----- left-shift by C2 (2) ----&gt;<sub>&nbsp;</sub></td></tr><tr><td>----- left-shift by C3 (3) ----&gt;<sub>&nbsp;</sub></td></tr></tbody></table></td><td><table><caption>State s'</caption><tbody><tr><td>s<sub>0,0</sub></td><td>s<sub>0,1</sub></td><td>s<sub>0,2</sub></td><td>s<sub>0,3</sub></td></tr><tr><td>s<sub>1,1</sub></td><td>s<sub>1,2</sub></td><td>s<sub>1,3</sub></td><td>s<sub>1,0</sub></td></tr><tr><td>s<sub>2,2</sub></td><td>s<sub>2,3</sub></td><td>s<sub>2,0</sub></td><td>s<sub>2,1</sub></td></tr><tr><td>s<sub>3,3</sub></td><td>s<sub>3,0</sub></td><td>s<sub>3,1</sub></td><td>s<sub>3,2</sub></td></tr></tbody></table></td></tr></tbody></table>

**The MixColumns transformation**

The mix columns (called _MixColumn_ in Rijndael) transformation uses a mathematical function to transform the values of a given column within a State, acting on the four values at one time as if they represented a four-term polynomial. In essence, if you think of MixColumns as a function, this could be written:

s'<sub>i,c</sub> = MixColumns (s<sub>i,c</sub>)

for 0 ≤ i ≤ 3 for some column, c. The column position doesn't change, merely the values within the column.

**Round Key generation and the AddRoundKey transformation**

The AES Cipher Key can be 128, 192, or 256 bits in length. The Cipher Key is used to derive a different key to be applied to the block during each round of the encryption operation. These keys are called the Round Keys and each will be the same length as the block, i.e., **Nb** 32-bit words (words will be denoted W).

The AES specification defines a key schedule by which the original Cipher Key (of length **Nk** 32-bit words) is used to form an _Expanded Key_. The Expanded Key size is equal to the block size times the number of encryption rounds plus 1, which will provide **Nr**+1 different keys. (Note that there are **Nr** encipherment rounds but **Nr**+1 AddRoundKey transformations.)

Consider that AES uses a 128-bit block and either 10, 12, or 14 iterative rounds depending upon key length. With a 128-bit key, for example, we would need 1408 bits of key material (128x11=1408), or an Expanded Key size of 44 32-bit words (44x32=1408). Similarly, a 192-bit key would require 1664 bits of key material (128x13), or 52 32-bit words, while a 256-bit key would require 1920 bits of key material (128x15), or 60 32-bit words. The key expansion mechanism, then, starts with the 128-, 192-, or 256-bit Cipher Key and produces a 1408-, 1664-, or 1920-bit Expanded Key, respectively. The original Cipher Key occupies the first portion of the Expanded Key and is used to produce the remaining new key material.

The result is an Expanded Key that can be thought of and used as 11, 13, or 15 separate keys, each used for one AddRoundKey operation. These, then, are the _Round Keys_. The diagram below shows an example using a 192-bit Cipher Key (**Nk**\=6), shown in _**magenta italics**_:

<table><tbody><tr><th>Expanded Key:</th><td><span color="magenta"><b><i>W<sub>0</sub></i></b></span></td><td><span color="magenta"><b><i>W<sub>1</sub></i></b></span></td><td><span color="magenta"><b><i>W<sub>2</sub></i></b></span></td><td><span color="magenta"><b><i>W<sub>3</sub></i></b></span></td><td><span color="magenta"><b><i>W<sub>4</sub></i></b></span></td><td><span color="magenta"><b><i>W<sub>5</sub></i></b></span></td><td>W<sub>6</sub></td><td>W<sub>7</sub></td><td>W<sub>8</sub></td><td>W<sub>9</sub></td><td>W<sub>10</sub></td><td>W<sub>11</sub></td><td>W<sub>12</sub></td><td>W<sub>13</sub></td><td>W<sub>14</sub></td><td>W<sub>15</sub></td><td>...</td><td>W<sub>44</sub></td><td>W<sub>45</sub></td><td>W<sub>46</sub></td><td>W<sub>47</sub></td><td>W<sub>48</sub></td><td>W<sub>49</sub></td><td>W<sub>50</sub></td><td>W<sub>51</sub></td></tr><tr><th>Round keys:</th><td colspan="4">Round key 0</td><td colspan="4">Round key 1</td><td colspan="4">Round key 2</td><td colspan="4">Round key 3</td><td>...</td><td colspan="4">Round key 11</td><td colspan="4">Round key 12</td></tr></tbody></table>

The AddRoundKey (called _Round Key addition_ in Rijndael) transformation merely applies each Round Key, in turn, to the State by a simple bit-wise exclusive OR operation. Recall that each Round Key is the same length as the block.

**Summary**

Ok, I hope that you've enjoyed reading this as much as I've enjoyed writing it — and now let me guide you out of the microdetail! Recall from the beginning of the AES overview that the cipher itself comprises a number of rounds of just a few functions:

-   _SubBytes_ takes the value of a word within a State and substitutes it with another value by a predefined S-box
-   _ShiftRows_ circularly shifts each row in the State by some number of predefined bytes
-   _MixColumns_ takes the value of a 4-word column within the State and changes the four values using a predefined mathematical function
-   _AddRoundKey_ XORs a key that is the same length as the block, using an Expanded Key derived from the original Cipher Key

```
Cipher (byte in[4*Nb], byte out[4*Nb], word w[Nb*(Nr+1)])

begin
  byte state[4,Nb]

  state = in

  AddRoundKey(state, w)

  for round = 1 step 1 to Nr-1
    SubBytes(state)
    ShiftRows(state)
    MixColumns(state)
    AddRoundKey(state, w+round*Nb)
  end for

  SubBytes(state)
  ShiftRows(state)
  AddRoundKey(state, w+Nr*Nb)

  out = state
end
```

**FIGURE 22: AES pseudocode.**

As a last and final demonstration of the operation of AES, Figure 22 is a pseudocode listing for the operation of the AES cipher. In the code:

-   _in\[\]_ and _out\[\]_ are 16-byte arrays with the plaintext and cipher text, respectively. (According to the specification, both of these arrays are actually 4\***Nb** bytes in length but **Nb**\=4 in AES.)
-   _state\[\]_ is a 2-dimensional array containing bytes in 4 rows and 4 columns. (According to the specification, this arrays is 4 rows by **Nb** columns.)
-   _w\[\]_ is an array containing the key material and is 4\*(**Nr**+1) words in length. (Again, according to the specification, the multiplier is actually **Nb**.)
-   _AddRoundKey()_, _SubBytes()_, _ShiftRows()_, and _MixColumns()_ are functions representing the individual transformations.

#### Cisco's Stream Cipher

Stream ciphers take advantage of the fact that:

x ⊕ y ⊕ y = x

One of the encryption schemes employed by Cisco routers to encrypt passwords is a stream cipher. It uses the following fixed keystream (thanks also to Jason Fossen for independently extending and confirming this string):

dsfd;kfoA,.iyewrkldJKDHSUBsgvca69834ncx

When a password is to be encrypted, the password function chooses a number between 0 and 15, and that becomes the offset into the keystream. Password characters are then XORed byte-by-byte with the keystream according to:

C<sub>i</sub> = P<sub>i</sub> ⊕ K<sub>(offset+i)</sub>

where K is the keystream, P is the plaintext password, and C is the ciphertext password.

Consider the following example. Suppose we have the password _abcdefgh_. Converting the ASCII characters yields the hex string 0x6162636465666768.

The keystream characters and hex code that supports an offset from 0 to 15 bytes and a password length up to 24 bytes is:

  d s f d ; k f o A , . i y e w r k l d J K D H S U B s g v c a 6 9 8 3 4 n c x  
0x647366643b6b666f412c2e69796577726b6c644a4b4448535542736776636136393833346e6378

Let's say that the function decides upon a keystream offset of 6 bytes. We then start with byte 6 of the keystream (start counting the offset at 0) and XOR with the password:

    0x666f412c2e697965  
 ⊕ 0x6162636465666768  
------------------
    0x070D22484B0F1E0D

The password would now be displayed in the router configuration as:

password 7 06070D22484B0F1E0D

where the "7" indicates the encryption type, the leading "06" indicates the offset into the keystream, and the remaining bytes are the encrypted password characters.

(Decryption is pretty trivial so that exercise is left to the reader. If you need some help with byte-wise XORing, see [_http://www.garykessler.net/library/byte\_logic\_table.html_](https://www.garykessler.net/library/byte_logic_table.html). If you'd like some programs that do this, see [_http://www.garykessler.net/software/index.html#cisco7_](https://www.garykessler.net/software/index.html#cisco7).)

#### TrueCrypt

TrueCrypt is an open source, on-the-fly crypto system that can be used on devices supports by Linux, MacOS, and Windows. First released in 2004, TrueCrypt can be employed to encrypt a partition on a disk or an entire disk.

___

> On May 28, 2014, the [TrueCrypt.org](http://www.truecrypt.org/) Web site was suddenly taken down and redirected to the SourceForge page. Although this paper is intended as a crypto tutorial and not a news source about crypto controversy, the sudden withdrawal of TrueCrypt cannot go without notice. The last stable release of TrueCrypt is v7.1a (February 2012); v7.2, released on May 28, 2014, only decrypts TrueCrypt volumes, ostensibly so that users can migrate to another solution. The TrueCryptNext (TCnext) Web page quickly went online at [TrueCrypt.ch](https://web.archive.org/web/20190605233018/https://truecrypt.ch/), using the tag line "TrueCrypt will not die" and noting that independent hosting in Switzerland guaranteed no product development interruption due to legal threats. The TCnext site became a repository of TrueCrypt v7.1a downloads and never released any subsequent software. The [TrueCrypt Wikipedia page](https://en.wikipedia.org/wiki/TrueCrypt) and accompanying references have some good information about the "end" of TrueCrypt as we knew it.
> 
> While there did not appear to be any rush to abandon TrueCrypt, it was also the case that you don't want to use old, unsupported software for too long. Another replacement was announced almost immediately upon the demise of TrueCrypt: "[TrueCrypt may live on after all as CipherShed.](http://www.ghacks.net/2014/09/19/truecrypt-may-live-on-after-all-as-ciphershed/)" The CipherShed group never produced a product, however, and the [CipherShed](https://ciphershed.org/) Web site no longer appeared to be operational sometime after October 2016. The only current, working fork of TrueCrypt appears to be [VeraCrypt](https://www.veracrypt.fr/en/Home.html), which is also open source, multi-platform, operationally identical to TrueCrypt, and compatible with TrueCrypt containers.
> 
> One final editorial comment. TrueCrypt was _**not**_ broken or otherwise compromised. It was withdrawn by its developers for reasons that have not yet been made public but there is no evidence to assume that TrueCrypt has been damaged in any way; on the contrary, two audits, completed in April 2014 and April 2015, found no evidence of backdoors or malicious code. See Steve Gibson's [TrueCrypt: Final Release Repository](https://www.grc.com/misc/truecrypt/truecrypt.htm) page for more information!

___

TrueCrypt uses a variety of encryption schemes, including AES, Serpent, and Twofish. A TrueCrypt volume is stored as a file that appears to be filled with random data, thus has no specific file signature. (It is true that a TrueCrypt container will pass a chi-square (Χ<sup>2</sup>) randomness test, but that is merely a general indicator of possibly encrypted content. An additional clue is that a TrueCrypt container will also appear on a disk as a file that is some increment of 512 bytes in size. While these indicators might raise a red flag, they don't rise to the level of clearly identifying a TrueCrypt volume.)

When a user creates a TrueCrypt volume, a number of parameters need to be defined, such as the size of the volume and the password. To access the volume, the TrueCrypt program is employed to find the TrueCrypt encrypted file, which is then mounted as a new drive on the host system.

![](https://www.garykessler.net/library/images/crypto_tc_Windows.png)

**FIGURE 23: TrueCrypt screen shot (Windows).**

![](https://www.garykessler.net/library/images/crypto_tc_Mac.png)

**FIGURE 24: TrueCrypt screen shot (MacOS).**

Consider this example where an encrypted TrueCrypt volume is stored as a file named _James_ on a thumb drive. On a Windows system, this thumb drive has been mounted as device _E:_. If one were to view the _E:_ device, any number of files might be found. The TrueCrypt application is used to mount the TrueCrypt file; in this case, the user has chosen to mount the TrueCrypt volume as device _K:_ (Figure 23). Alternatively, the thumb drive could be used with a Mac system, where it has been mounted as the _/Volumes/JIMMY_ volume. TrueCrypt mounts the encrypted file, _James_, and it is now accessible to the system (Figure 24).

![](https://www.garykessler.net/library/images/crypto_tc_hidden-volume.gif)

**FIGURE 25: TrueCrypt hidden encrypted volume within an encrypted volume. _(From http://www.truecrypt.org/images/docs/hidden-volume.gif)_**

One of the most interesting — certainly one of the most controversial — features of TrueCrypt is called _plausible deniability_, protection in case a user is "compelled" to turn over the encrypted volume's password. When the user creates a TrueCrypt volume, he/she chooses whether to create a standard or hidden volume. A standard volume has a single password, while a hidden volume is created within a standard volume and uses a second password. As shown in Figure 25, the unallocated (free) space in a TrueCrypt volume is always filled with random data, thus it is impossible to differentiate a hidden encrypted volume from a standard volume's free space.

To access the hidden volume, the file is mounted as shown above and the user enters the hidden volume's password. When under duress, the user would merely enter the password of the standard (i.e., non-hidden) TrueCrypt volume.

More information about TrueCrypt can be found at the [TCnext Web Site](https://web.archive.org/web/20190605233018/https://truecrypt.ch/) or in the [_TrueCrypt User's Guide (v7.1a)_](https://www.garykessler.net/library/crypto/TrueCrypt%20User%20Guide.pdf).

___

> An active area of research in the digital forensics community is to find methods with which to detect hidden TrueCrypt volumes. Most of the methods do not detect the presence of a hidden volume, per se, but infer the presence by left over forensic remnants. As an example, both MacOS and Windows systems usually have a file or registry entry somewhere containing a cached list of the names of mounted volumes. This list would, naturally, include the name of TrueCrypt volumes, both standard and hidden. If the user gives a name to the hidden volume, it would appear in such a list. If an investigator were somehow able to determine that there were two TrueCrypt volume names but only one TrueCrypt device, the inference would be that there was a hidden volume. A good summary paper that also describes ways to infer the presence of hidden volumes — at least on some Windows systems — can be found in "[Detecting Hidden Encrypted Volumes](https://link.springer.com/chapter/10.1007%2F978-3-642-13241-4_21)" (Hargreaves & Chivers).
> 
> Having nothing to do with TrueCrypt, but having something to do with plausible deniability and devious crypto schemes, is a new approach to holding password cracking at bay dubbed _Honey Encryption_. With most of today's crypto systems, decrypting with a wrong key produces digital gibberish while a correct key produces something recognizable, making it easy to know when a correct key has been found. Honey Encryption produces fake data that resembles real data for every key that is attempted, making it significantly harder for an attacker to determine whether they have the correct key or not; thus, if an attacker has a credit card file and tries thousands of keys to crack it, they will obtain thousands of possibly legitimate credit card numbers. See "['Honey Encryption' Will Bamboozle Attackers with Fake Secrets](https://www.technologyreview.com/2014/01/29/14160/honey-encryption-will-bamboozle-attackers-with-fake-secrets/)" (Simonite) for some general information or "[Honey Encryption: Security Beyond the Brute-Force Bound](http://pages.cs.wisc.edu/~rist/papers/HoneyEncryptionpre.pdf)" (Juels & Ristenpart) for a detailed paper.

___

#### Encrypting File System (EFS)

Microsoft introduced the Encrypting File System (EFS) with the NTFS v3.0 file system and has supported EFS since Windows 2000 and XP (although EFS was and is not supported in all variations of Windows). Since Windows 10, EFS can also be used on FAT and exFAT volumes. EFS can be used to encrypt individual files, directories, or entire volumes. While disabled by default, EFS encryption can be easily enabled via File Explorer (aka Windows Explorer) by right-clicking on the file, directory, or volume to be encrypted, selecting Properties, Advanced, and _Encrypt contents to secure data_ (Figure 26). Note that encrypted files and directories are displayed in green in Windows Explorer.

![](https://www.garykessler.net/library/images/crypto_efs_WindowsExplorer.png)

**FIGURE 26: EFS and Windows (File) Explorer.**

The Windows command prompt provides an easy tool with which to detect EFS-encrypted files on a disk. The cipher command has a number of options, but the /u/n switches can be used to list all encrypted files on a drive (Figure 27).

![](https://www.garykessler.net/library/images/crypto_efs_cipher.png)

**FIGURE 27: The cipher command.**

EFS supports a variety of secret key encryption schemes, including 3DES, DESX, AES, and ECC, as well as RSA public key encryption. The operation of EFS — at least at the theoretical level — is relatively straight-forward. When a file is saved to disk:

-   A random File Encryption Key (FEK) is generated by the operating system.
-   The file contents are encrypted using one of the SKC schemes and the FEK.
-   The FEK is stored with the file, encrypted with the owner's RSA public key. In addition, the FEK is encrypted with the RSA public key of any other authorized users and, optionally, a recovery agent's RSA public key.

When the file is opened:

-   The FEK is recovered using the RSA private key of the owner, other authorized user, or the recovery agent.
-   The FEK is used to decrypt the file's contents.

There are weaknesses with the system, most of which are related to key management. As an example, the RSA private key can be stored on an external device such as a floppy disk (yes, really!), thumb drive, or smart card. In practice, however, this is rarely done; the user's private RSA key is often stored on the hard drive. In addition, early EFS implementations (prior to Windows XP SP2) tied the key to the username; later implementations employ the user's password.

A more serious implementation issue is that a backup file named _esf0.tmp_ is created prior to a file being encrypted. After the encryption operation, the backup file is deleted — not wiped — leaving an unencrypted version of the file available to be undeleted. For this reason, it is best to use encrypted directories because the temporary backup file is protected by being in an encrypted directory.

![](https://www.garykessler.net/library/images/crypto_efs_header.gif)

**FIGURE 28: EFS key storage. _(Source: [NTFS.com](http://www.ntfs.com/attribute-encrypted-files.htm))_**

The EFS information is stored as a named stream in the $LOGGED\_UTILITY\_STREAM Attribute (attribute type 256 \[0x100\]). This information includes (Figure 28):

-   A Data Decryption Field (DDF) for each user authorized to decrypt the file, containing the user's Security Identifier (SID), the FEK encrypted with the user's RSA public key, and other information.
-   A Data Recovery Field (DRF) with the encrypted FEK for every method of data recovery

```
Master File Table (MFT) Parser V2.1 - Gary C. Kessler (11 June 2013)
   :
   :
0056-0059  Attribute type: 0x10-00-00-00 [$STANDARD_INFORMATION]
0060-0063  Attribute length: 0x60-00-00-00 [96 bytes]
0064       Non-resident flag: 0x00 [Attribute is resident]
   :
   :
0152-0155  Attribute type: 0x30-00-00-00 [$FILE_NAME]
0156-0159  Attribute length: 0x70-00-00-00 [112 bytes]
0160       Non-resident flag: 0x00 [Attribute is resident]
   :
   :
0264-0267  Attribute type: 0x40-00-00-00 [$VOLUME_VERSION/$OBJECT_ID]
0268-0271  Attribute length: 0x28-00-00-00 [40 bytes]
0272       Non-resident flag: 0x00 [Attribute is resident]
   :
   :
0304-0307  Attribute type: 0x80-00-00-00 [$DATA]
0308-0311  Attribute length: 0x48-00-00-00 [72 bytes]
0312       Non-resident flag: 0x01 [Attribute is non-resident]
0313       Name length: 0x00 [0 16-bit characters]
0314-0315  Offset to name: 0x00-00 [0 bytes]
0316-0317  Flag: 0x00-40 -- Encrypted
   :
   :
0376-0379  Attribute type: 0x00-01-00-00 [$LOGGED_UTILITY_STREAM]
0380-0383  Attribute length: 0x50-00-00-00 [80 bytes]
0384       Non-resident flag: 0x01 [Attribute is non-resident]
   :
0440-0447  Name: 0x24-00-45-00-46-00-53-00 [$EFS]
   :
   :
```

**FIGURE 29: The $LOGGED\_UTILITY\_STREAM Attribute (see complete output at [crypto\_EFS.txt](https://www.garykessler.net/library/images/crypto_EFS.txt)).**

Files in an NTFS file system maintain a number of attributes that contain the system metadata (e.g., the $STANDARD\_INFORMATION attribute maintains the file timestamps and the $FILE\_NAME attribute contains the file name). Files encrypted with EFS store the keys, as stated above, in a data stream named $EFS within the $LOGGED\_UTILITY\_STREAM attribute. Figure 29 shows the partial contents of the Master File Table (MFT) attributes for an EFS encrypted file.

#### Some of the Finer Details of RC4

RC4 is a variable key-sized stream cipher developed by Ron Rivest in 1987. RC4 works in output-feedback (OFB) mode, so that the key stream is independent of the plaintext. The algorithm is described in detail in Schneier's _Applied Cryptography_, 2/e, pp. 397-398 and the Wikipedia [_RC4_](https://en.wikipedia.org/wiki/RC4) article.

RC4 employs an 8x8 substitution box (S-box). The S-box is initialized so that S\[i\] = i, for i=(0,255).

A permutation of the S-box is then performed as a function of the key. The K array is a 256-byte structure that holds the key (possibly supplemented by an Initialization Vector), repeating itself as necessary so as to be 256 bytes in length (obviously, a longer key results in less repetition). **\[\[NOTE: All arithmetic below is assumed to be on a per-byte basis and so is implied to be _modulo 256_.\]\]**

   j = 0  
   for i = 0 to 255  
     j = j + S\[i\] + K\[i\]  
     swap (S\[i\], S\[j\])

Encryption and decryption are performed by XORing a byte of plaintext/ciphertext with a random byte from the S-box in order to produce the ciphertext/plaintext, as follows:

   Initialize i and j to zero

For each byte of plaintext (or ciphertext):

   i = i + 1  
   j = j + S\[i\]  
   swap (S\[i\], S\[j\])  
   z = S\[i\] + S\[j\]

   Decryption: plaintext \[i\] = S\[z\] ⊕ ciphertext \[i\]  
   Encryption: ciphertext \[i\] = S\[z\] ⊕ plaintext \[i\]

A Perl implementation of RC4 (fine for academic, but not production, purposes) can be found at [_http://www.garykessler.net/software/index.html#RC4_](https://www.garykessler.net/software/index.html#RC4). This program is an implementation of the [CipherSaber](https://en.wikipedia.org/wiki/CipherSaber) version of RC4, which employs an initialization vector (IV). The [CipherSaber IV](http://ciphersaber.gurus.org/) is a 10-byte sequence of random numbers between the value of 0-255. The IV is placed in the first 10-bytes of the encrypted file and is appended to the user-supplied key (which, in turn, can only be up to 246 bytes in length).

In 2014, Rivest and Schuldt developed a redesign of RC4 called [_Spritz_](http://people.csail.mit.edu/rivest/pubs/RS14.pdf). The main operation of Spritz is similar to the main operation of RC4, except that a new variable, _w_, is added:

   i = i + w  
   j = k + S \[j + S\[i\]\]  
   k = i + k + S\[j\]  
   swap (S\[i\], S\[j\])  
   z = (S\[j + S\[i + S\[z+k\]\]\]

   Decryption: plaintext \[i\] = S\[z\] ⊕ ciphertext \[i\]  
   Encryption: ciphertext \[i\] = S\[z\] ⊕ plaintext \[i\]

As seen above, RC4 has two pointers into the S-box, namely, _i_ and _j_; Spritz adds a third pointer, _k_.

Pointer _i_ move slowly through the S-box; note that it is incremented by 1 in RC4 and by a constant, _w_, in Spritz. Spritz allows _w_ to take on any odd value, ensuring that it is always relatively prime to 256. (In essence, RC4 sets _w_ to a value of 1.)

The other pointer(s) — _j_ in RC4 or _j_ and _k_ in Spritz — move pseudorandomly through the S-box. Both ciphers have a single swap of entries in the S-box. Both also produce an output byte, _z_, as a function of the other parameters. Spritz, additionally, includes the previous value of _z_ as part of the calculation of the new value of _z_.

#### Challenge-Handshake Authentication Protocol (CHAP)

CHAP, originally described in [RFC 1994](https://www.rfc-editor.org/info/rfc1994), and its variants (e.g., Microsoft's MS-CHAP) are authentication schemes that allow two parties to demonstrate knowledge of a shared secret without actually divulging that shared secret to a third party who might be eavesdropping.

![](https://www.garykessler.net/library/images/crypto_chap.png)

**FIGURE 30: CHAP Handshake.**

The operation of CHAP is relatively straight-forward (Figure 30). Assume that the Client is logging on to a remote Server across the Internet. The Client needs to prove to the Server that it knows the password but doesn't want to reveal the password in any form that an eavesdropper can decrypt. In CHAP:

1.  The User sends their username (in plaintext) to the Server.
2.  The Server sends some random _challenge_ string (i.e., some number of octets) to the User.
Based upon the password and some algorithm, the User generates an encrypted _response_ string (the same length as the _challenge_) and sends it to the Server.-   The Server looks up the User's password in it's database and, using the same algorithm, generates an expected _response_ string.
-   The Server compares its expected _response_ to the actual _response_ sent by the User. If the two match, the User is authenticated.

Since the password is never revealed to a third-party, why can't we then just keep the same password forever? Note that CHAP is potentially vulnerable to a known plaintext attack; the _challenge_ is plaintext and the _response_ is encrypted using the password and a known CHAP algorithm. If an eavesdropper has enough _challenge_/_response_ pairs, they might well be able to determine the password. Some other issues related to this form of authentication can be found in "Off-Path Hacking: The Illusion of Challenge-Response Authentication" (Gilad, Y., Herzberg, A., & Shulman, H., September-October 2014, _IEEE Security & Privacy_, _12_(5), 68-77).

#### Secure E-mail and S/MIME

Electronic mail and messaging are the primary applications for which people use the Internet. Obviously, we want our e-mail to be secure; but, what exactly does that mean? And, how do we accomplish this task?

There are a variety of ways to implement or access secure e-mail and cryptography is an essential component to the security of electronic mail. And, the good news is that we have already described all of the essential elements in the sections above. From a practical perspective, secure e-mail means that once a sender sends an e-mail message, it can only be read by the intended recipient(s). That can only be accomplished if the encryption is end-to-end; i.e., the message must be encrypted before leaving the sender's computer and cannot be decrypted until it arrives at the recipient's system. But in addition to privacy, we also need the e-mail system to provide authentication, non-repudiation, and message integrity — all functions that are provided by a combination of hash functions, secret key crypto, and public key crypto. Secure e-mail services or software, then, usually provide two functions, namely, message signing and message encryption. Encryption, obviously, provides the secrecy; signing provides the rest.

[Figure 4](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#fig04), above, shows how the three different types of crypto schemes work together. For purposes of e-mail, however, it is useful to independently examine the functions of signing and encryption, if for no other reason than while secure e-mail applications and services can certainly sign and encrypt a message, they may also have the ability to sign a message without encrypting it or encrypt a message without signing it.

E-mail messages are signed for the purpose of authenticating the sender, providing a mechanism so that the sender cannot later disavow the message (i.e., non-repudiation), and proving message integrity — unless, of course, the sender claims that their key has been stolen. The steps of signing and verifying e-mail are shown in Figure 31. To sign a message:

1.  The sender's software examines the message body.
2.  Information about the sender is retrieved (e.g., the sender's private key).
3.  The signing operation (e.g., encrypting the hash of the message with the sender's private key) is performed.
4.  The Digital Signature is appended to the e-mail message.
5.  The signed e-mail message is sent.

Verification of the signed message requires the receiver's software to perform the opposite steps as the sender's software. In short, the receiver extracts the sender's Digital Signature, calculates a digital signature based upon the sender's information (e.g., using the sender's public key), and compares the computed signature with the received signature; if they match, the message's signature is verified. Note that if there are multiple recipients of the message, each will perform the same steps to verify the signature because the verification is based upon the sender's information (compare this to decryption, described below).

E-mail messages are encrypted for the purpose of privacy, secrecy, confidentiality — whatever term you wish to use to indicate that the message is supposed to be a secret between sender and receiver. The steps of encrypting and decrypting e-mail are shown in Figure 32. To encrypt a message:

1.  The sender's software examines the message body.
2.  The sender's software pulls out specific information about the recipient...
3.  ... and the encryption operation is performed.
4.  The encrypted message replaces the original plaintext e-mail message.
5.  The encrypted e-mail message is sent.

Note that if the message has multiple recipients, the encryption step will yield different results because the encryption step is dependent upon the recipient's information (e.g., their public key). One application might choose to send a different encrypted message to each recipient; another might send the same encrypted message to each recipient, but encrypt the decryption key differently for each recipient (note further that this latter approach might allow a recipient to perform a known plaintext attack against the other recipients; each recipient knows the decryption key for the message and also sees the key encrypted with the recipient's information). In any case, recipient-specific information (e.g., their private key) must be used in order to decrypt the message and the decryption steps performed by the recipient are essentially the opposite of those performed by the sender.

This discussion, so far, has been a little vague because different applications will act is different ways but, indeed, are performing very similar generic steps. There are two primary ways for a user to get send and receive secure e-mail, namely, to employ some sort of Web-based e-mail service or employ a secure e-mail client.

![](https://www.garykessler.net/library/images/crypto_4SecureMail.png)

**FIGURE 33: E-mail message to non-4SecureMail user.**

If the reader is interested in using a Web-based secure e-mail service, you have only to do an Internet search to find many such services. All have slightly different twists to them, but here are a few representative free and commercial options:

-   [4SecureMail](http://www.4securemail.com/): Web-based e-mail service using 128-bit SSL between client and server; also supports many e-mail clients and mobile apps. Anonymous headers are "virtually untraceable." Non-4SecureMail recipients are notified by e-mail of waiting secure message which can be downloaded via browser; authenticity of the message is via the user's registered WaterMark (Figure 33).
    
-   [CounterMail](https://countermail.com/): Online, end-to-end e-mail service based upon OpenPGP. Multi-platform support, including Android. Has a USB key option, requiring use of a hardware dongle in order to retrieve mail.
    
-   [Hushmail](https://www.hushmail.com/): Web- or client-based, end-to-end encrypted email based upon OpenPGP. Multi-platform support, including iPhone. Can send secure e-mail to non-Hushmail user by employing a shared password.
    
-   [Kolab Now](https://kolabnow.com/): Web-based secure e-mail service provider although it does not perform server-side e-mail encryption; they recommend that users employ a true end-to-end e-mail encryption solution. Data is stored on servers exclusively located in Switzerland and complies with the strict privacy laws of that country.
    
-   [ProtonMail](https://proton.me/mail): End-to-end secure e-mail service using AES and OpenPGP, also located in Switzerland. Does not log users' IP addresses, thus provides an anonymous service. Multi-platform support, plus Android and iOS.
    
-   [Tutanota](https://tutanota.com/): Web-, Android-, or iOS-based end-to-end secure e-mail service.
    

The alternative to using a Web-based solution is to employ a secure e-mail client or, at least, a client that supports secure e-mail. Using host-based client software ensures end-to-end security — as long as the mechanisms are used correctly. There are no lack of clients that support secure mechanisms; Apple Mail, Microsoft Outlook, and Mozilla Thunderbird, for example, all have native support for S/MIME and have plug-ins that support OpenPGP/GPG (see [Section 5.5](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#pgp) for additional information on the signing and encryption capabilities of PGP).

The Secure Multipurpose Internet Mail Extensions (S/MIME) protocol is an IETF standard for use of public key-based encryption and signing of e-mail. S/MIME is actually a series of extensions to the MIME protocol, adding digital signature and encryption capability to MIME messages (which, in this context, refers to e-mail messages and attachments). S/MIME is based upon the original IETF MIME specifications and RSA's PKCS #7 secure message format, although it is now an IETF specification defined primarily in four RFCs:

-   [RFC 3369](https://www.rfc-editor.org/info/rfc3369): Cryptographic Message Syntax (CMS) (based upon PKCS #7) — Describes the syntax (format) used to digitally sign, digest, authenticate, or encrypt any type of message content, the rules for encapsulation, and an architecture for certificate-based key management.
-   [RFC 3370](https://www.rfc-editor.org/info/rfc3370): Cryptographic Message Syntax (CMS) Algorithms — Describes the use of common crypto algorithms to support the CMS, such as those for message digests (e.g., MD5 and SHA-1), signatures (e.g., DSA and RSA), key management, and content encryption (e.g., RC2 and 3DES).
-   [RFC 3850](https://www.rfc-editor.org/info/rfc3850): Secure/Multipurpose Internet Mail Extensions (S/MIME) Version 3.1 Certificate Handling — Specifies how S/MIME agents use the Internet X.509 Public Key Infrastructure (PKIX) and X.509 certificates to send and receive secure MIME messages. (See [Section 4.3](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#pkcca) for additional information about X.509 certificates.)
-   [RFC 3851](https://www.rfc-editor.org/info/rfc3851): Secure/Multipurpose Internet Mail Extensions (S/MIME) Version 3.1 Message Specification — Describes the "secure" part of the S/MIME protocol, add digital signature and encryption services to MIME. The MIME standard specifies the general structure for different content types within Internet messages; this RFC specifies cryptographically-enhanced MIME body parts.

A quite good overview of the protocol can be found in a Microsoft TechNet article titled "[Understanding S/MIME](https://technet.microsoft.com/en-us/library/aa995740(v=exchg.65).aspx)."

```
Content-Type: multipart/signed;
   boundary="Apple-Mail=_6293E5DF-2993-4264-A32B-01DD43AB4259";
   protocol="application/pkcs7-signature"; micalg=sha1

--Apple-Mail=_6293E5DF-2993-4264-A32B-01DD43AB4259
Content-Transfer-Encoding: 7bit
Content-Type: text/plain;
charset=us-ascii

Hi Carol.

What was that pithy Groucho Marx quote?

/kess

--Apple-Mail=_6293E5DF-2993-4264-A32B-01DD43AB4259
Content-Disposition: attachment;
filename=smime.p7s
Content-Type: application/pkcs7-signature;
name=smime.p7s
Content-Transfer-Encoding: base64

MIAGCSqGSIb3DQEHAqCAMIACAQExCzAJBgUrDgMCGgUAMIAGCSqGSIb3DQEHAQAAoIIJ9TCCBK8w
ggOXoAMCAQICEQDgI8sVEoNTia1hbnpUZ2shMA0GCSqGSIb3DQEBCwUAMG8xCzAJBgNVBAYTAlNF
MRQwEgYDVQQKEwtBZGRUcnVzdCBBQjEmMCQGA1UECxMdQWRkVHJ1c3QgRXh0ZXJuYWwgVFRQIE5l
dHdvcmsxIjAgBgNVBAMTGUFkZFRydXN0IEV4dGVybmFsIENBIFJvb3QwHhcNMTQxMjIyMDAwMDAw
WhcNMjAwNTMwMTA0ODM4WjCBmzELMAkGA1UEBhMCR0IxGzAZBgNVBAgTEkdyZWF0ZXIgTWFuY2hl
c3RlcjEQMA4GA1UEBxMHU2FsZm9yZDEaMBgGA1UEChMRQ09NT0RPIENBIExpbWl0ZWQxQTA/BgNV
BAMTOENPTU9ETyBTSEEtMjU2IENsaWVudCBBdXRoZW50aWNhdGlvbiBhbmQgU2VjdXJlIEVtYWls
IENBMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAibEN2npTGU5wUh28VqYGJre4SeCW
51Gr8fBaE0kVo7SMG2C8elFCp3mMpCLfF2FOkdV2IwoU00oCf7YdCYBupQQ92bq7Fv6hh6kuQ1JD
FnyvMlDIpk9a6QjYz5MlnHuI6DBk5qT4VoD9KiQUMxeZrETlaYujRgZLwjPU6UCfBrCxrJNAubUI
kzqcKlOjENs9IGE8VQOO2U52JQIhKfqjfHF2T+7hX4Hp+1SA28N7NVK3hN4iPSwwLTF/Wb1SN7Az
aS1D6/rWpfGXd2dRjNnuJ+u8pQc4doykqTj/34z1A6xJvsr3c5k6DzKrnJU6Ez0ORjpXdGFQvsZA
P8vk4p+iIQIDAQABo4IBFzCCARMwHwYDVR0jBBgwFoAUrb2YejS0Jvf6xCZU7wO94CTLVBowHQYD
VR0OBBYEFJJha4LhoqCqT+xn8cKj97SAAMHsMA4GA1UdDwEB/wQEAwIBhjASBgNVHRMBAf8ECDAG
AQH/AgEAMB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDBDARBgNVHSAECjAIMAYGBFUdIAAw
RAYDVR0fBD0wOzA5oDegNYYzaHR0cDovL2NybC51c2VydHJ1c3QuY29tL0FkZFRydXN0RXh0ZXJu
YWxDQVJvb3QuY3JsMDUGCCsGAQUFBwEBBCkwJzAlBggrBgEFBQcwAYYZaHR0cDovL29jc3AudXNl
cnRydXN0LmNvbTANBgkqhkiG9w0BAQsFAAOCAQEAGypurFXBOquIxdjtzVXzqmthK8AJECOZD8Vm
am+x9bS1d14PAmEA330F/hKzpICAAPz7HVtqcgIKQbwFusFY1SbC6tVNhPv+gpjPWBvjImOcUvi7
BTarfVil3qs7Y+Xa1XPv7OD7e+Kj//BCI5zKto1NPuRLGAOyqC3U2LtCS5BphRDbpjc06HvgARCl
nMo6x59PiDRuimXQGoq7qdzKyjbR9PzCZCk1r9axp3ER0gNDsY8+muyeMlP0dpLKhjQHuSzK5hxK
2JkNwYbikJL7WkJqIyEQ6WXH9dW7fuqMhSACYurROgcsWcWZM/I4ieW26RZ6H3kU9koQGib6fIr7
mzCCBT4wggQmoAMCAQICEQDvRS7kXtfrf7+LzZ7ZrvObMA0GCSqGSIb3DQEBCwUAMIGbMQswCQYD
VQQGEwJHQjEbMBkGA1UECBMSR3JlYXRlciBNYW5jaGVzdGVyMRAwDgYDVQQHEwdTYWxmb3JkMRow
GAYDVQQKExFDT01PRE8gQ0EgTGltaXRlZDFBMD8GA1UEAxM4Q09NT0RPIFNIQS0yNTYgQ2xpZW50
IEF1dGhlbnRpY2F0aW9uIGFuZCBTZWN1cmUgRW1haWwgQ0EwHhcNMTcwMTAxMDAwMDAwWhcNMTgw
MTAxMjM1OTU5WjAkMSIwIAYJKoZIhvcNAQkBFhNnY2tAZ2FyeWtlc3NsZXIubmV0MIIBIjANBgkq
hkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA0ql2BQ6RbvG/9L4qGN7x9v3ieA5YFu0NKGF91J4ujNT5
upPhZl0K2u5Oa7Zj1WEf+1Jq+D7oj4px7C8nkJfJAyUVgZ9DfHysEliF4l4xSsVMwNUuAoWIYpfe
iGKEL/X9U323yv2NNxkMTfiZ3WWsETNL43zzNwRGnu7hZmqpQsbNaIfhEXvC5BVZXv/B38jrq/v+
6szdcvnjbhC+gkaaxNlj7xvJiVom0RESIqwwSkVNcn2KFu/Uja6NMzVApbkwwJfmTgEHm8jgiIEc
+CHkeqkWF3u3HdE8a2xn8Xeg6IKlekZCtnEPwURF/IwWYq4BPdYKZIBkp9QQ5yr7KL4BVwIDAQAB
o4IB8TCCAe0wHwYDVR0jBBgwFoAUkmFrguGioKpP7GfxwqP3tIAAwewwHQYDVR0OBBYEFJW+t4wQ
BPSM0gdWl+x1+F76ThzAMA4GA1UdDwEB/wQEAwIFoDAMBgNVHRMBAf8EAjAAMCAGA1UdJQQZMBcG
CCsGAQUFBwMEBgsrBgEEAbIxAQMFAjARBglghkgBhvhCAQEEBAMCBSAwRgYDVR0gBD8wPTA7Bgwr
BgEEAbIxAQIBAQEwKzApBggrBgEFBQcCARYdaHR0cHM6Ly9zZWN1cmUuY29tb2RvLm5ldC9DUFMw
XQYDVR0fBFYwVDBSoFCgToZMaHR0cDovL2NybC5jb21vZG9jYS5jb20vQ09NT0RPU0hBMjU2Q2xp
ZW50QXV0aGVudGljYXRpb25hbmRTZWN1cmVFbWFpbENBLmNybDCBkAYIKwYBBQUHAQEEgYMwgYAw
WAYIKwYBBQUHMAKGTGh0dHA6Ly9jcnQuY29tb2RvY2EuY29tL0NPTU9ET1NIQTI1NkNsaWVudEF1
dGhlbnRpY2F0aW9uYW5kU2VjdXJlRW1haWxDQS5jcnQwJAYIKwYBBQUHMAGGGGh0dHA6Ly9vY3Nw
LmNvbW9kb2NhLmNvbTAeBgNVHREEFzAVgRNnY2tAZ2FyeWtlc3NsZXIubmV0MA0GCSqGSIb3DQEB
CwUAA4IBAQAeZNE03prooHZKvzjDUbVFGfiX+pTPJxQjruFazJOK7iHd3PaadSlqBiyMJgazDBYY
hvsjzrM0p9KV5z6pTyA8aWczvNTq+MgDymTPpCYOG6GSUHFIWok+iv/MVrJ/zhOgk81n9MuHe02t
XYtg5r9UbEXpIOfhNt5ziMo9r37uHKl+kF+y5fF4YHyVcmlFGg0gzpOSdpvMdFWfpKm03jvpBITQ
WDMrQ2vJs4vQUq0Qh+13cbOMc4cPHAAz+YXtcR2IiUPDDEfAPcgDSYfYeQlNHiGYVqWA9xGL9cDQ
iyB1zp5TERWIfv2BnNdodupn+8LtoIX/cOJLyf16F8Lp1xBzMYIDxjCCA8ICAQEwgbEwgZsxCzAJ
BgNVBAYTAkdCMRswGQYDVQQIExJHcmVhdGVyIE1hbmNoZXN0ZXIxEDAOBgNVBAcTB1NhbGZvcmQx
GjAYBgNVBAoTEUNPTU9ETyBDQSBMaW1pdGVkMUEwPwYDVQQDEzhDT01PRE8gU0hBLTI1NiBDbGll
bnQgQXV0aGVudGljYXRpb24gYW5kIFNlY3VyZSBFbWFpbCBDQQIRAO9FLuRe1+t/v4vNntmu85sw
CQYFKw4DAhoFAKCCAekwGAYJKoZIhvcNAQkDMQsGCSqGSIb3DQEHATAcBgkqhkiG9w0BCQUxDxcN
MTcwMTAxMTgwNDIwWjAjBgkqhkiG9w0BCQQxFgQUrhgwhfaU8cFbOsDvQEcl78xWH2wwgcIGCSsG
AQQBgjcQBDGBtDCBsTCBmzELMAkGA1UEBhMCR0IxGzAZBgNVBAgTEkdyZWF0ZXIgTWFuY2hlc3Rl
cjEQMA4GA1UEBxMHU2FsZm9yZDEaMBgGA1UEChMRQ09NT0RPIENBIExpbWl0ZWQxQTA/BgNVBAMT
OENPTU9ETyBTSEEtMjU2IENsaWVudCBBdXRoZW50aWNhdGlvbiBhbmQgU2VjdXJlIEVtYWlsIENB
AhEA70Uu5F7X63+/i82e2a7zmzCBxAYLKoZIhvcNAQkQAgsxgbSggbEwgZsxCzAJBgNVBAYTAkdC
MRswGQYDVQQIExJHcmVhdGVyIE1hbmNoZXN0ZXIxEDAOBgNVBAcTB1NhbGZvcmQxGjAYBgNVBAoT
EUNPTU9ETyBDQSBMaW1pdGVkMUEwPwYDVQQDEzhDT01PRE8gU0hBLTI1NiBDbGllbnQgQXV0aGVu
dGljYXRpb24gYW5kIFNlY3VyZSBFbWFpbCBDQQIRAO9FLuRe1+t/v4vNntmu85swDQYJKoZIhvcN
AQEBBQAEggEAi3e2rGbbeg0pkV1oaPVU/90jYkcqt3tYGNf5cDgySklQQARxuXhPpIroLLEKexrM
m96Z5jB+c2Lo2LIZG9O7gFQkDF3wqU5YOB5m1PmTdQXaYjhttIKGAHHQs1vW0fhAXHIBRBI0d3j9
wK9svbxG7XwxDwZ9ED/tv4dbI9gyktbl6ppczAEC6+JoTGdLLPtEI9XgME9ZBh3ykXa87o46MgdQ
FCTp5J2z1BMeP9B7FENvN9D+fi297HjCTFZoxKopoLQ92/Cud2w85+s6JbgeWlqAdNgAp4c24bxW
Winw3N7LukDaWB1KdkfWKMsP2bJXF9uIDoPYTjvb2VO4kVEaBwAAAAAAAA==
--Apple-Mail=_6293E5DF-2993-4264-A32B-01DD43AB4259--
```

**FIGURE 34: Sample multipart/signed message.**

Figure 34 shows a sample signed message using S/MIME. The first few lines indicate that this is a multipart signed message using the PKCS #7 signature protocol and, in this case, the SHA-1 hash. The two text lines following the first \--Apple-Mail=... indicates that the message is in plaintext; this is followed by the actual message. The next block indicates use of S/MIME where the signature block is in an attached file (the _.p7s_ extension indicates that this is a signed-only message), encoded using BASE64.

```
Content-Type: application/pkcs7-mime; name=smime.p7m; smime-type=enveloped-data
Content-Transfer-Encoding: base64

MIAGCSqGSIb3DQEHA6CAMIACAQAxggHOMIIBygIBADCBsTCBmzELMAkGA1UEBhMCR0IxGzAZBgNV
BAgTEkdyZWF0ZXIgTWFuY2hlc3RlcjEQMA4GA1UEBxMHU2FsZm9yZDEaMBgGA1UEChMRQ09NT0RP
IENBIExpbWl0ZWQxQTA/BgNVBAMTOENPTU9ETyBTSEEtMjU2IENsaWVudCBBdXRoZW50aWNhdGlv
biBhbmQgU2VjdXJlIEVtYWlsIENBAhEA70Uu5F7X63+/i82e2a7zmzANBgkqhkiG9w0BAQEFAASC

AQAbKAO7BoEKgZ9e/C837YpZYzspGdLbiMnRPmz3p2v+8H9DgPcOzMAjwdvT94el3hguke/Dn4LK
L4/Un9ZbruoPzfps0Cxa8A+Acw2fVluYImKs0y3zCCOCQiKwW4IMWmS0HCvFTrHKGuGcWmpaFUv1
vTdFNPhQzF6jRJPv35GNtHWEFIRwqWFG1jOh/0uX0o3Cg8B1j/wwjTEkd0WU/DzYbe6nQSJzh7Kz
9guBAyfSVPZsLcvkd1ftP4vVrILnafKaFK9ls3al8dT5+oY7oTUHhem+oPMLcOnX0+ZZcqs97+oW
HvQQy1lpofU8b/0Qt8lYZfAC5lMIOg6nMbmIUdjOMIAGCSqGSIb3DQEHATAUBggqhkiG9w0DBwQI
hd7YXnfGNOeggAQYdIYlsYppSpTleDWPMbopt2Zu7+umGjuHBAiuLupldkuMbwQYorVXhHJ3J6G3
0Rad+eC3vbpu4Ohtcz/rBBAhZ97y8YgLhmSJzXCSLh5UBAisFO1grQ/WgQQIpwnzeRUrRikEMMo/
qTMXMBaFFmGRHgSbVaNTJOcpk11kmrZbUWUdSWUZwQ3TSdvnyVUJUUf7gFs8eQQop2w/yLoGZGVY
DJFpaCBEbgBZAXbWeducGiDR8lUYOPdwjSnfb96yBgQIN2WZZMiZ6x4ECMQJ5uftbc+dBAj/LNyO
TGk4awAAAAAAAAAAAAA=
```

**FIGURE 35: Sample S/MIME encrypted message.**

Figure 35 shows a sample encrypted message, carried as an S/MIME enveloped data attachment (_.p7m_) file, also formatted in BASE64. S/MIME can also attach certificate management messages (_.p7c_) and compressed data (_.p7z_). Many more S/MIME sample messages can be found in [RFC 4134](https://www.rfc-editor.org/info/rfc4134) ("Examples of S/MIME Messages").

![](https://www.garykessler.net/library/images/crypto_S-MIME_certificate.png)

**FIGURE 36: Sample S/MIME certificate.**

S/MIME is a powerful mechanism and is widely supported by many e-mail clients. To use your e-mail client's S/MIME functionality, you will need to have an S/MIME certificate (Figure 36). Several sites provide free S/MIME certificates for personal use, such as [Instant SSL](https://www.instantssl.com/products/ssl-trial-ssl-certificate-tls) (Comodo), [Secorio](https://www.secorio.com/en/?S_MIME_Email_Certificates), and [StartSSL](https://www.startssl.com/) (StartCom); commercial-grade S/MIME certificates are available from many other CAs. (NOTE: If these sites install your S/MIME certificate to your browser, you might need to export \[backup\] the certificate and import it so it can be seen by your e-mail application.)

Do note that S/MIME is not necessarily well-suited for use with Web-based e-mail services. First off, S/MIME is designed for true end-to-end (i.e., client-to-client) encryption and Web mail services provide server-to-server or server-to-client encryption. Second, while S/MIME functionality could be built into browsers, the end-to-end security offered by S/MIME requires that the private key be accessible only to the end-user and not to the Web server. Finally, end-to-end encryption makes it impossible for a third-party to scan e-mail for viruses and other malware, thus obviating one of the advantages of using a Web-based e-mail service in the first place.

#### Identity-Based Encryption (IBE)

Identity-Based Encryption is a public-key crypto scheme that can be used for key authentication by parties who do not have an _a priori_ relationship. First proposed in 1984 by Adi Shamir, a sender can encrypt a message using the receiver's public key, which can be derived from an ASCII character string that represents some unique identifier of the receiver (e.g., an e-mail address, telephone number, or a ship's registry number).

IBE employs most of the concepts that we already know from other PKC schemes. First, every user has a private/public key pair, where the public key is widely distributed and the private key is a closely held secret. Second, while the keys in the key pair are mathematically related, a party that knows someone's public key cannot easily derive the matching private key. Finally, key distribution and management requires a trusted third-party — called the _Private Key Generator (PKG)_ — but, unlike the certificate authority (CA) model, does not require an _a priori_ relationship between the users and the PKG.

In an IBE system, the PKG generates its own master public/private key pair. The PKG can publish and distribute the _Master Public Key_, along with the algorithm used to derive keys; the PKG closely holds the _Master Private Key_ as a secret. Any user in the IBE system can generate a public key using a unique identifying string (_ID_) in combination with the Master Public Key and the key-generation algorithm. In order to obtain a private key, the owner of the identifying string contacts the PKG which, in turn, derives the appropriate private key from the ID in combination with the Master Private Key.

By way of example, consider the case where Alice wants to send an encrypted message to Bob. Bob's email address, _bobby@example.boats_, is his unique ID string. Using the PKG's Master Public Key, Alice can derive Bob's public key from his ID string. With that, she can encrypt a message for Bob and send it to him. When Bob receives the message, he contacts the PKG and, using some mechanism specified by the PKG, authenticates himself as the true owner of the ID string. The PKG can then use Bob's ID string and the Master Private Key to derive his private key, which is then sent to Bob. At that point, Bob can decrypt Alice's message. Once Bob has his private key, of course, he can continue to use it for subsequent messages.

Using a similar but reverse process, Alice could use her own unique ID string (e.g., _alice@example.wine_) to obtain her private key from the PKG with which she could digitally sign the e-mail message that she sends to Bob. Bob would then use Alice's ID string to derive her public key in order to verify the signature.

In 2001, [Dan Boneh](https://crypto.stanford.edu/ibe/) from Stanford University and Matt Franklin from the University of California, Davis developed a practical implementation of IBE based on elliptic curves and a mathematical construct called the [Weil Pairing](https://crypto.stanford.edu/~dabo/papers/bfibe.pdf). Clifford Cocks, from the U.K.'s Government Communications Headquarters (GCHQ), described another IBE solution based on quadratic residues in composite groups, also in 2001.

![](https://www.garykessler.net/library/images/crypto_ibe.png)

**FIGURE 37: Identity-based encryption schema.**

Boneh and Franklin defined four basic functions that an IBE system needs to employ (Figure 37):

1.  **Setup:** The setup algorithm is run once by the PKG to establish the IBE environment. This is the stage where the Master Public Key (**_K<sub>pub</sub>_**) and Master Private Key (**_K<sub>m</sub>_**) are created. The Master Public Key and certain system parameters (**_P_**), such as the legal message space, legal ciphertext space, and key-derivation algorithm, can be shared widely, while **_K<sub>m</sub>_** is kept secret by the PKG.
    
2.  **Extract:** When a user requests their private key, the PKG executes the extract algorithm. This is the step where the PKG verifies that the requesting party is the authentic owner of the **_ID_** string, although the specific method for doing this is actually outside the IBE protocol. The PKG uses the ID string to derive the user's private key (**_d_**) from the Master Private Key and sends it to the requesting party. In this example, Bob is requesting his private key and offers up his ID string, **_ID<sub>B</sub>_**. The PKG derives his private key, **_d<sub>B</sub>_** from the system parameters (**_P_**), the Master Private Key (**_K<sub>m</sub>_**), and Bob's ID string; this is shown in the figure as **_d<sub>B</sub>_** = f(**_P_**,**_K<sub>m</sub>_**,**_ID_<sub>B</sub>**). Alice would follow the same process to extract her private key.
    
3.  **Encrypt:** In order for Alice to encrypt a message for Bob, she needs to obtain Bob's public key, **_e<sub>B</sub>_**, which can be derived from the system parameters (**_P_**), the Master Public Key (**_K<sub>pub</sub>_**), and Bob's ID string. This is shown in the figure as **_e<sub>B</sub>_** = f(**_P_**,**_K<sub>pub</sub>_**,**_ID_<sub>B</sub>**). Now armed with Bob's public key, Alice can encrypt the plaintext message (**_m_**) using Bob's public key; the output is the ciphertext string (**_c_**), shown in the figure as **_c_** = **_e_<sub>B</sub>**(**_m_**).
    
4.  **Decrypt:** Bob decrypts the ciphertext string using his previously extracted private key, yielding the plaintext message. This is shown in the figure as **_m_** = **_d_<sub>B</sub>**(**_c_**).
    

The most practical IBE algorithms are based on elliptical curves, and include [Boneh-Franklin (BF-IBE),](https://en.wikipedia.org/wiki/Boneh%E2%80%93Franklin_scheme) [Sakai-Kasahara (SK-IBE)](https://en.wikipedia.org/wiki/Sakai%E2%80%93Kasahara_scheme), and [Boneh-Boyen (BB-IBE)](https://link.springer.com/chapter/10.1007%2F978-3-540-24676-3_14). Cocks' solution causes so much overhead (i.e., ciphertext expansion) as to be impractical expect for very short messages, such as key exchange for a secret key crypto scheme. (This is, ironically, where PKC schemes find themselves, in general; in the 1970s, PKC took up to three orders of magnitude more compute time than an SKC scheme to encrypt a message, thus the use of PKC for key exchange.) [RFC 5091: Identity-Based Cryptography Standard (IBCS) #1](https://www.rfc-editor.org/info/rfc5091) describes an implementation of IBE using Boneh-Franklin (BF) and Boneh-Boyen (BB1) Identity-based Encryption.

IBE has its pros and cons but is particularly useful in very dynamic environments where pre-distribution of public keys is not possible or not feasible. It requires, of course, that the PKG is highly trusted. The PKG is also a lucrative target for hackers because compromise of the PKG compromises every message sent by parties employing the system.

#### Shamir's Secret Sharing (SSS)

Shamir's Secret Sharing is a rather novel secret-sharing method where a group can work together to gain access to a resource. Adi Shamir, of RSA fame, described a method whereby a secret is divided into _n_ parts in such a way that knowledge of any _k_ parts allows the secret to be revealed; knowledge of any _k_\-1 or fewer parts yields no information about the secret at all. This so-called (_k_,_n_) _threshold scheme_ has many applications in real life. Consider this simple example from Shamir's original 1979 paper, "[How to Share a Secret](https://scholar.google.com/scholar_url?url=https://dl.acm.org/doi/pdf/10.1145/359168.359176%3Fcasa_token%3DJkSyXGq9KusAAAAA:RJKjGPM9S-sebnBPS57ctqM-GnWGsV3luwg5vKr6VIXr-n3ALqsOlb2C3CeKwEIU014uig3tENFI&hl=en&sa=T&oi=ucasa&ct=ucasa&ei=SJxnX6jWDsTCmAH7yJyQAQ&scisig=AAGBfm0ksiANj0NkBo0wx9_qQrvKYvFAmQ)":

> Consider ... a company that digitally signs all its checks... If each executive is given a copy of the company's secret signature key, the system is convenient but easy to misuse. If the cooperation of all the company's executives is necessary in order to sign each check, the system is safe but inconvenient. The standard solution requires at least three signatures per check, and it is easy to implement with a (3, n) threshold scheme. Each executive is given a small magnetic card with one D<sub>i</sub> piece, and the company's signature generating device accepts any three of them in order to generate (and later destroy) a temporary copy of the actual signature key D. The device does not contain any secret information and thus it need not be protected against inspection. An unfaithful executive must have at least two accomplices in order to forge the company's signature in this scheme.

The idea is conceptually straight-forward. Observe that a polynomial of degree _k_\-1 is defined by _k_ terms; e.g., the 2nd-degree polynomial 4x<sup>2</sup>+6x+15 has three terms. Suppose the secret, _S_, can be expressed as a number. We choose a prime number, _P_, so that _S_<_P_. A (_k_,_n_) threshold scheme requires 0<_k_≤_n_<_P_ and is defined by the following polynomial of degree _k_\-1:

f(x) = _a_<sub>0</sub> + _a_<sub>1</sub>x + _a_<sub>2</sub>x<sup>2</sup> + ... + _a_<sub><i>k</i>-1</sub>x<sup><i>k</i>-1</sup>

> where _a_<sub>0</sub>\=_S_ and the remaining coefficients are random positive integers such that _a_<sub>i</sub><_P_, for _i_\=1..._k_\-1.

We can now build a table of _n_ values of the polynomial; i.e., f(_i_) for _i_ = 1..._n_.

At this point, each of the _n_ participants in the (_k_,_n_) scheme is given a different value of _i_ in the range 1-_n_, as well as the corresponding integer output, f(_i_). If any subset of _k_ participants puts their information together, they can reconstruct a polynomial of degree _k_\-1 so that the _a_<sub>0</sub> term takes on the value of the original secret, _S_. This is based on the fact that the system is designed so that the secret, divided into parts, can be estimated — or interpolated — by knowledge of any _k_ of the parts.

So, here is a simple, but reasonably accurate, example of how SSS works. (It is simplified because it will employ integer arithmetic whereas the SSS scheme actually employs finite field arithmetic, resulting in a less than totally secure system; nevertheless, the reader should get the idea.) Let's build a (3,5) threshold system; i.e., one where the secret is split across five pieces and any three are required to reconstruct the secret. In this case, the secret, _S_, is represented by the number 18. We will choose 23 as the prime number _P_ (which is larger than _S_, as required).

Since _k_\=3, we need to create a polynomial of degree 2. This polynomial will need three coefficients; _a_<sub>0</sub>\=_S_, and we will randomly choose _a_<sub>1</sub>\=6 and _a_<sub>2</sub>\=11 (as required, all values of _a_ are smaller than _P_.) We now have the polynomial:

f(_x_) = 18 + 6_x_ + 11_x_<sup>2</sup>

The next step is to create the table of the five outputs of the function, f(_x_), for all values _x_\=1,...,5; each (_x_,f(_x_)) pair is denoted _T_<sub><i>x</i></sub>:

_T_<sub>1</sub> = (1,35)  
_T_<sub>2</sub> = (2,74)  
_T_<sub>3</sub> = (3,135)  
_T_<sub>4</sub> = (4,218)  
_T_<sub>5</sub> = (5,323)

Each of the five participants is provided with one of the _T_ pairs. Now, let's see what happens when participants 1, 3, and 4, for example, get together to reconstruct the secret.

The SSS scheme relies on the fact that _k_ points can be used to estimate the original _n_\-part problem. For the reconstruction, we need to compute Lagrange polynomials — denoted _L_ — which are used for polynomial interpolation. The Lagrange method finds the lowest degree polynomial that represents a function that coincides at a set of given points. In this example, we have three points: (_x_<sub>0</sub>,_y_<sub>0</sub>)=_T_<sub><i>1</i></sub>\=(1,35), (_x_<sub>1</sub>,_y_<sub>1</sub>)=_T_<sub><i>3</i></sub>\=(3,135), and (_x_<sub>2</sub>,_y_<sub>2</sub>)=_T_<sub><i>4</i></sub>\=(4,218).

The formulas and solutions for the three Lagrange polynomials are:

![](https://www.garykessler.net/library/images/crypto_math_sss1.png) 

The next step is to solve the Lagrange problem:

![](https://www.garykessler.net/library/images/crypto_math_sss2.png) 

It turns out that the only coefficient that matters is the one for the _x_<sup>0</sup> (ones) term, which is 18. This, of course, is the _a_<sub>0</sub> coefficient which, by design, is the secret, _S_. This also points to a computational shortcut, namely, we can ignore any calculation involving any part of the polynomial other than the _x_<sup>0</sup> term. Now, the calculation is merely:

f(_x_) = (35 ⋅ 2) + (135 ⋅ -2) + (218 ⋅ 1) = 18 = _S_

Shamir's original paper describes this pretty well and the [Wikipedia page](https://en.wikipedia.org/wiki/Shamir%27s_Secret_Sharing) has a very detailed example.

#### Bit integrity, CRCs, and Hashes

Hash functions are used in crypto systems for message integrity, so that Bob can be ensured that the message he receives is the one that Alice sent. Hash functions do this by detecting bit errors in messages; even a single bit error in a large message will cause a significant change in the hash value. Hash functions, however, were not originally designed for message integrity; back in the early days of computer communications, longitudinal redundancy check (LRC) and cyclic redundancy check (CRC) algorithms, including the use of parity bits, were employed for bit error detection.

An LRC code is pretty trivial and operates on the bytes of the message. Generally, an LRC might merely take the exclusive-OR of all of the bytes in the message or compute the sum of all of the bytes, and append the checksum value to end of the message. While there are many variants of this, here are two examples. Suppose my message is the character string:

My name is Gary.

Using the ASCII character code, this message would appear in hexadecimal as 4D79206E616D6520697320476172792E. Taking the XOR of each byte would yield an LRC of 0x0A; summing the individual bytes (modulo 256) would yield a sum of 0x9C. When transmitting the message, the LRC bytes are appended to the end of the message. The receiver also calculates the LRC on the message and the receiver's value should match the transmitted value.

LRCs are very weak error detection mechanisms. If there is a single bit error, it will certainly be detected by an LRC. But an even number of errors in the same bit position within multiple bytes will escape detection by an XOR LRC. And a burst of errors might even escape detection by an additive LRC. The point is, it is trivial to create syndromes of bit errors that won't be found by an LRC code. (This is not to say that they are not used in some data transmission systems!)

CRCs were developed in the early 1960s to provide message integrity, bit-error detection, and, in some cases, bit-error correction in data communication systems. There are many CRC codes in use today, almost all in some sort of networking application. CRCs are expressed as an n-order polynomial yielding an n-bit result; i.e., a CRC-n polynomial is one with n+1 terms and is used to compute an n-bit checksum. An n-bit CRC code can be used with an arbitrary length input, can detect 100% of burst errors up to n bits in length, and can detect bursts of n bits or more with probability (1-2<sup>-n</sup>).

Common CRCs today yield checksums of 8, 16, 32, or 64 bits in length although other lengths are also used in some applications. Two example 16-bit CRCs are:

-   _CRC-16-ANSI:_ x<sup>16</sup> + x<sup>15</sup> + x<sup>2</sup> + 1 (used in DECnet, Modbus, and USB)
-   _CRC-16-CCITT:_ x<sup>16</sup> + x<sup>12</sup> + x<sup>5</sup> + 1 (used in Bluetooth, HDLC, and X.25)

If we take our message above, for example, the CRC-CCITT checksum value would be 0xBF9D.

![](https://www.garykessler.net/library/images/crypto_crc.png)

**FIGURE 38: Hardware CRC-16-CCITT generator. _(From Microchip AN730)_**

CRCs are, in essence, a one-way stream cipher, receiving one bit of the message at a time, and using XOR gates and an n-bit shift register to compute the checksum. The contents of the register are the checksum value, where the register is generally initialized as all zeroes or all ones. This operation can be seen by looking at the hardware implementation of the CRC-16-CCITT checksum (Figure 38), taken from [Microchip Application Note 730 (AN730)](https://ww1.microchip.com/downloads/en/AppNotes/00730a.pdf).

Now, this stroll down memory lane (at least for me!) is really about why LRCs and CRCs are _not_ used in cryptography. While they work fine for bit error detection and the fact that an arbitrary-length input produces a fixed-length output, they were not designed for the level of robustness required for cryptographic error detection and other functions. The origins of cryptographic hash functions (CHFs) date back to the early 1950s, when hash functions — aka, message digests — were used in computer science to map an arbitrary length data item to a bit array of fixed size; e.g., one application was to map variable-length variable names to a fixed-size token array for processing variables in a programming language's compiler or interpreter. It took another 20 years for hashing to be applied to cryptography.

Like a CRC, a CHF algorithm incorporated into a modern cryptosystem (say, starting in the late-1980s or early-1990s) can, theoretically, accept a message of arbitrary length and produce a fixed-length output. And, like CRCs, it is believed that it is computationally infeasible to produce two messages having the same message digest value or to produce a message having a given prespecified message digest value, _given a strong message digest algorithm_. Unlike CRCs, message digest algorithms are intended for digital signature applications; signing the "compressed" version of a message (i.e., the hash) is far more efficient than signing the entire message. Furthermore, signing the hash value takes the same amount of time regardless of the size of the message, thus making the time required for signing to be much more predictable than if signing an arbitrary length message. Also unlike CRCs, message digest algorithms have an encryption function integral to their computation.

Among the earliest of the hash algorithms incorporated into cryptosystems was Message Digest 5, described in 1992 in [RFC 1321](https://www.rfc-editor.org/info/rfc1321); the Secure Hash Algorithm came later, detailed in [RFC 3174](https://www.rfc-editor.org/info/rfc3174) in 2001. MD5 and SHA-1 produce a 128-bit and 160-bit output, respectively; MD5 accepts a message of arbitrary length while SHA-1 limits the input to 2<sup>64</sup>\-1 bits. The string above would generate the following hash values:

MD5 ("My name is Gary.") = 3b55c9c3503c456906b765fbaaf37223  
SHA1 ("My name is Gary.") = e41d178d27d53066a7c87f5a422d74156a8c27b4

While hash algorithms are designed to be as simple as possible, they are much more complex than a CRC. So, here's a rundown on part of the MD5 calculation to give an idea of the manipulations. See the RFCs for sample code.

First off, MD5 operates on 128-bit blocks or, more precisely, four 32-bit words, at one time. Therefore, the initial step is to pad the incoming message so that it is an even multiple of 16 32-bit words (512 bits). The algorithm then steps through the input 128 bits at a time. I'll explain the process below and you'll see multiple passes over the data with a lot of transpositions and substitutions, which are the basic operations of encryption that were described earlier in this paper.

So, here is some basic MD5 terminology and nomenclature:

-   The 128-bit block is divided into four 32-bit words. These values are stored in four registers, called A, B, C, and D. The MD5 specification provides the initial values of these registers prior to any calculation.
  
-   MD5 defines four "auxiliary" functions — denoted F, G, H, and I — which are the guts of the transformation process. Each of these functions takes three 32-bit values (denoted x, y, and z below) as input and produces one 32-bit output. The functions are defined below in terms of the Boolean AND (•), OR (∨), XOR (⊕), and NOT (¬) functions (see this [table of byte-based Boolean operations](https://www.garykessler.net/library/byte_logic_table.html) if you want to perform any of these operations by hand):

-   F (x,y,z) = (x • y) ∨ (¬x • z)
-   G (x,y,z) = (x • z) ∨ (y • ¬z)
-   H (x,y,z) = x ⊕ y ⊕ z
-   I (x,y,z) = y ⊕ (x ∨ ¬z)


-   All of the arithmetic is performed modulo 2<sup>32</sup>.
  
-   One of the operations on the registers is called a _circular shift_, denoted by the "<<<" operator. The nomenclature "x <<< s" means to rotate (or circularly shift) the contents of the 32-bit x register by s bits to the left; bits that "fall off" on the left side of the register wrap around to the right side.
  
-   There is a table of constants, denoted T, with 64 entries computed by applying the sine function to the values 1 to 64. (See the MD5 specification for details.)
  
-   The 16-byte array, X, contains the 128-bit block that is being processed during a given round of operation.

The MD5 algorithm processes one 128-bit block as a time, sequentially stepping through the message. There is a feedback mechanism in place because all computations use the A, B, C, and D registers without zeroing them out between blocks. There are four rounds of processing on each 128-bit block, and each round comprises 16 operations. Each of the 64 operations uses the following generic formula:

a = b + ((a + f(b,c,d) + X\[k\] + T\[i\]) <<< s)

where:

-   a, b, c, and d represent one of the 32-bit registers, A, B, C, or D, in the order specified below;
-   f(b,c,d) represents one of the auxiliary functions, F, G, H, or I, as specified below, and using three 32-bit registers (b, c, and, d) as input;
-   X\[k\] is the k-th byte (0-15) of the current 128-bit (16-byte) block, where k is specified below; and
-   T\[i\] is the i-th entry (1-64) in the T table, where i is specified below.

The table below summarizes the characteristics of each operation within each round; remember that we start with operations 1-16 in round 1, then move on to round 2, etc.:

<table><caption><b>TABLE 9. MD5 rounds and operations.</b></caption><tbody><tr><th rowspan="2">Register<br>Order</th><th rowspan="18"></th><th colspan="4">ROUND 1<br>Function F</th><th rowspan="18"></th><th colspan="4">ROUND 2<br>Function G</th><th rowspan="18"></th><th colspan="4">ROUND 3<br>Function H</th><th rowspan="18"></th><th colspan="4">ROUND 4<br>Function I</th></tr><tr><th>Op.</th><th>k</th><th>i</th><th>s</th><th>Op.</th><th>k</th><th>i</th><th>s</th><th>Op.</th><th>k</th><th>i</th><th>s</th><th>Op.</th><th>k</th><th>i</th><th>s</th></tr><tr><td rowspan="4">ABCD</td><td>1</td><td>0</td><td>1</td><td rowspan="4">7</td><td>1</td><td>1</td><td>17</td><td rowspan="4">5</td><td>1</td><td>5</td><td>33</td><td rowspan="4">4</td><td>1</td><td>0</td><td>49</td><td rowspan="4">6</td></tr><tr><td>2</td><td>4</td><td>5</td><td>2</td><td>5</td><td>21</td><td>2</td><td>1</td><td>37</td><td>2</td><td>12</td><td>53</td></tr><tr><td>3</td><td>8</td><td>9</td><td>3</td><td>9</td><td>25</td><td>3</td><td>13</td><td>41</td><td>3</td><td>8</td><td>57</td></tr><tr><td>4</td><td>12</td><td>13</td><td>4</td><td>13</td><td>29</td><td>4</td><td>9</td><td>45</td><td>4</td><td>4</td><td>61</td></tr><tr><td rowspan="4">DABC</td><td>5</td><td>1</td><td>2</td><td rowspan="4">12</td><td>5</td><td>6</td><td>18</td><td rowspan="4">9</td><td>5</td><td>8</td><td>34</td><td rowspan="4">11</td><td>5</td><td>7</td><td>50</td><td rowspan="4">10</td></tr><tr><td>6</td><td>5</td><td>6</td><td>6</td><td>10</td><td>22</td><td>6</td><td>4</td><td>38</td><td>6</td><td>3</td><td>54</td></tr><tr><td>7</td><td>9</td><td>10</td><td>7</td><td>14</td><td>26</td><td>7</td><td>0</td><td>42</td><td>7</td><td>15</td><td>58</td></tr><tr><td>8</td><td>13</td><td>14</td><td>8</td><td>2</td><td>30</td><td>8</td><td>12</td><td>46</td><td>8</td><td>11</td><td>62</td></tr><tr><td rowspan="4">CDAB</td><td>9</td><td>2</td><td>3</td><td rowspan="4">17</td><td>9</td><td>11</td><td>19</td><td rowspan="4">14</td><td>9</td><td>11</td><td>35</td><td rowspan="4">16</td><td>9</td><td>14</td><td>51</td><td rowspan="4">15</td></tr><tr><td>10</td><td>6</td><td>7</td><td>10</td><td>15</td><td>23</td><td>10</td><td>7</td><td>39</td><td>10</td><td>10</td><td>55</td></tr><tr><td>11</td><td>10</td><td>11</td><td>11</td><td>3</td><td>27</td><td>11</td><td>3</td><td>43</td><td>11</td><td>6</td><td>59</td></tr><tr><td>12</td><td>14</td><td>15</td><td>12</td><td>7</td><td>31</td><td>12</td><td>15</td><td>47</td><td>12</td><td>2</td><td>63</td></tr><tr><td rowspan="4">BCDA</td><td>13</td><td>3</td><td>4</td><td rowspan="4">22</td><td>13</td><td>0</td><td>20</td><td rowspan="4">20</td><td>13</td><td>14</td><td>36</td><td rowspan="4">23</td><td>13</td><td>5</td><td>52</td><td rowspan="4">21</td></tr><tr><td>14</td><td>7</td><td>8</td><td>14</td><td>4</td><td>24</td><td>14</td><td>10</td><td>40</td><td>14</td><td>1</td><td>56</td></tr><tr><td>15</td><td>11</td><td>12</td><td>15</td><td>8</td><td>28</td><td>15</td><td>6</td><td>44</td><td>15</td><td>13</td><td>60</td></tr><tr><td>16</td><td>15</td><td>16</td><td>16</td><td>12</td><td>32</td><td>16</td><td>2</td><td>48</td><td>16</td><td>9</td><td>64</td></tr></tbody></table>

The way to interpret the information in the table is as follows. Remember, the generic formula for all operations is:

a = b + ((a + f(b,c,d) + X\[k\] + T\[i\]) <<< s)

So, if you want to understand, say, operation 11 in Round 3, the table says:

-   The register order is CDAB, so a=C, b=D, c=A, and d=B
-   Use function H, where registers D, A, and B will be the inputs (in that order)
-   k = 3, i = 43, and s = 16

Given these values, the operation for this round would be computed as:

C = D + ((C + H(D,A,B) + X\[3\] + T\[43\]) <<< 16)

#### Keyed-Hash Message Authentication Code (HMAC)

IPsec authentication (for both AH and ESP), the Signal protocol, and many other communication schemes use _HMAC_, a Keyed-Hashing Message Authentication Code, to allow two users to mutually authenticate by demonstrating that they both know a secret without actually explicitly exchanging that secret. Described in [FIPS PUB 198](http://csrc.nist.gov/publications/fips/fips198/fips-198a.pdf) and [RFC 2104](https://www.rfc-editor.org/info/rfc2104), HMAC uses a shared secret key between two parties rather than public key methods for message authentication. The advantage of this method of authentication is a simplicity in processing; PKC algorithms generally require three to five orders of magnitude (i.e., 1,000-100,000 times) more processing than an SKC scheme operating over the same message. If an application requires nothing more than authentication and needs fast processing, HMACs are an attractive alternative. The generic HMAC procedure can be used with just about any hash algorithm.

In HMAC, both parties share a secret key. The secret key will be employed with the hash algorithm in a way that provides mutual authentication without transmitting the key on the communications channel. Some key management procedure, external to the HMAC protocol, is used to manage key exchange between the two parties.

Recall that hash functions operate on fixed-size blocks of input at one time; MD5 and SHA-1, for example, work on 64-byte blocks. These functions then generate a fixed-size hash value; MD5 and SHA-1 produce 16-byte (128-bit) and 20-byte (160-bit) output strings, respectively. For use with HMAC, the secret key (K) should be at least as long as the hash output.

![](https://www.garykessler.net/library/images/crypto_hmac.png)

**FIGURE 39: Keyed-hash MAC operation.**

The following steps provide a simplified, although reasonably accurate, description of how the HMAC scheme would work with a particular plaintext MESSAGE (Figure 39):

1.  Alice pads K so that it is as long as an input block; call this padded key K<sub>p</sub>. Alice computes the hash of the padded key concatenated (.) with the message, i.e., HASH (K<sub>p</sub>.MESSAGE).
2.  Alice transmits MESSAGE and the hash value.
3.  Bob has also padded K to create K<sub>p</sub>. He computes HASH (K<sub>p</sub>.MESSAGE) on the incoming message.
4.  Bob compares the computed hash value with the received hash value. If they match, then the sender — Alice — must know the secret key and her identity is, thus, authenticated.

HMACs are ideal for communication between two parties, say, Alice and Bob, who have a shared secret. They don't work as well in any sort of multicast environment, because all of the receivers — and there could be millions — would all have a single shared secret key. In a zero-trust environment, this greatly raises the opportunity for a rogue group member to send bogus messages. The common solution to this problem, of course, is to use a PKC methodology and employ a digital signature; Alice, for example, creates a signature with her private key and Bob, Carol, Dave, and all the other receivers can verify the signature using Alice's public key. In this case, none of the receivers who hold Alice's public key can spoof being Alice because they can't generate her signature. Indeed, the digital signature also provides the property of non-repudiation.

The problem with digital signatures is that they require a lot of computation for both sender and receiver(s); remember, PKC can require at least 1,000 times more processing for a given message compared to an SKC scheme. The resultant signature also reduces the bandwidth efficiency on a communications channel. So, digitally signing messages in a broadcast or large multicast environment where receivers want to authenticate each transmission is a challenge. (See the next section on TESLA for one possible solution.)

#### Timed Efficient Stream Loss-tolerant Authentication (TESLA)

The Timed Efficient Stream Loss-tolerant Authentication (TESLA) protocol, described in [RFC 4082](https://www.rfc-editor.org/info/rfc4082), provides a bandwidth-efficient, fault-tolerant mechanism so that a single key can be used for authentication by a large number of receivers in a multicast/broadcast environment. TESLA uses an HMAC-type scheme to provide time-delayed message authentication. The basic idea is that the sender uses a key for only a certain time interval (e.g., one second); during that time interval, receivers buffer all of the incoming messages. During the next time interval, the sender shares the previous interval's key with the receivers and starts to use a new key; receivers can now authenticate the buffered messages from the prior time interval even as they buffer new incoming messages.

TESLA requires the sender to generate a chain of authentication keys, where a given key is associated with a single time slot, T. In general, T<sub>i+1</sub> = T<sub>i</sub>+Δt. The sender can create as many keys as it wants but might need to limit the length of the chain based upon memory or other constraints. So, suppose the sender wants to create a chain of N keys. The sender will randomly select the N-th (last) key in the chain, K<sub>N</sub>. Then, using a pseudo-random number generator (PRNG) function, _P_, and the last key value generated as the seed, the sender creates the prior key in the chain. Thus, K<sub>N-1</sub> = _P_(K<sub>N</sub>), K<sub>N-2</sub> = _P_(K<sub>N-1</sub>),..., K<sub>0</sub> = _P_(K<sub>1</sub>). Each key is assigned to a time interval, so that K<sub>i</sub> is associated with T<sub>i</sub>. One important feature is that this is a one-way chain; given any key, K<sub>i</sub>, all previously used keys can be derived by the receiver (i.e., any K<sub>j</sub> can be calculated where j<i) but no following keys can be predicted (i.e., no K<sub>j</sub> can be guessed where j>i).

The operation of TESLA is relatively straight-forward. During the T<sub>0</sub> time slot, the sender employs an HMAC where K<sub>0</sub> is the secret key. These messages are buffered by one or more receivers. When the T<sub>1</sub> time slot starts, the sender sends K<sub>0</sub> to the receiver(s) and starts to use K<sub>1</sub> as the secret key. The receiver(s) can now authenticate the T<sub>0</sub> buffered messages. Because of the properties of the one-way chain, the receiver(s) can derive keys from previous time slots that might have been lost due to transmission errors, thus providing fault tolerance.

For obvious reasons, TESLA requires loosely synchronized clocks between the sender and the receivers, but is not really intended for real-time services that cannot tolerate any delay. Check out the RFC or the paper by Perrig, Canetti, Tygar, and Song in _[RSA CryptoBytes](https://www.readkong.com/page/the-tesla-broadcast-authentication-protocol-5530155)_ for more detail. A light-weight version of the protocol, called µTESLA, was designed for sensor networks that have limited processing power, limited memory, and a real-time communication requirement. This version provides nearly immediate distribution of the authentication key and RC5 encryption. µTESLA is described in a paper by Perrig, Szewczyk, Tygar, Wen, and Culler in the _[ACM Journal of Wireless Networks](https://people.eecs.berkeley.edu/~tygar/papers/SPINS/SPINS_wine-journal.pdf)_.

### QUANTUM CRYPTOGRAPHY

When this paper was originally prepared in the late-1990s, most crypto schemes were designed using basic integer arithmetic, logarithms and exponents, and prime number factorization, all for what today can only be described as _classic_ computers. Resistance to brute-force attacks was provided by the expediency of using very large keys and requiring periodic changes to the key in order to "outrun" the available computing power necessary to try and guess every possible key value within the keyspace. Quantum computers, quantum algorithms, and quantum cryptography are changing the paradigms of modern and future cryptography. I am not an expert in this area so I am not going to talk about this subject in any great detail, but here is a quick intro and some pointers to additional information.

Today's computers employ a _von Neumann architecture_, based upon the design model introduced by Johnny von Neumann in the 1940s. Quantum computing requires an entirely new way of looking at computer hardware. The most fundamental difference between a classic computer and a quantum computer is the concept of a _bit_. In classic computers, a bit — aka _binary digit_ — can take on only two values, namely, 0 or 1. A combination of two bits can take on four values — 00, 01, 10, and 11. To generalize, an n-bit string can take on 2<sup>n</sup> possible values, 0-(2<sup>n</sup>\-1).

While classic computers define bits based upon storing data as electrical signals, quantum computers define storage based upon electrons and photons that can encode the data in multiple states. Due to a phenomenon known as _superposition_, a _quantum bit_, or _qubit_, can take on a state that is some combination of both 0 and 1 simultaneously; i.e., neither 0 nor 1, but something much more complicated. This means that two qubits can take on the four values mentioned above — 00, 01, 10, and 11 — _at the same time!_ In theory, such a computer can solve problems too complex for conventional computers.

![](https://www.garykessler.net/library/images/crypto_qubit.png)

**FIGURE 40: Classical computing bit and quantum computing qubit. _(Strathclyde University)_**

There are many sources of information about quantum computing online and in various journal papers and articles. Here are a few places where you can start:

-   [Quantum Computing page](https://qoqms.phys.strath.ac.uk/research_qc.html) at University of Strathclyde
-   [Quantum Computing page](https://en.wikipedia.org/wiki/Quantum_computing) at Wikipedia
-   Kietzmann, J., Demetis, D.S., Eriksson, T., & Dabirian, A. (2021, July-August). Hello Quantum! How Quantum Computing Will Change the World. _IT Professional_, _23_(4), 106-111. DOI: [10.1109/MITP.2021.3086917](https://doi.org/10.1109/MITP.2021.3086917)
-   Middleton , C. (2021, March 5). What's Under the Hood of a Quantum Computer? _Physics Today_. DOI: [10.1063/PT.6.1.20210305a](https://www.doi.org/10.1063/PT.6.1.20210305a)

In theory, a quantum computer can solve problems that are too computationally complex for a today's conventional computers. The implications for employing quantum methods to attack classic cryptography algorithms should be readily apparent. Since we can theoretically build a computer where an n-qubit device can take on 2<sup>n</sup> states at the same time, it renders an n-bit keyspace susceptible to a nearly immediate brute-force attack. Before any panic sets in, recognize that quantum computers today are relatively small, so a large keyspace (say, 256 bits or larger) is as safe today from a quantum computer brute-force attack as a smaller key (e.g., 128 bits or smaller) is against a brute-force attack from a classic computer. Clearly, there will be this battle for some time keeping the key size large enough to withstand quantum attacks. (This is similar to the increasing size of RSA keys to keep them computationally infeasible to factor.) Meanwhile, a functional quantum computer that can pose a serious attack on cryptographic schemes as we know them today does not exist, and won't for many years — if ever. That said, it would be folly to ignore the potential threat and be blindsided.

Intertestingly, symmetric cryptography is generally considered quantum-safe. Grover's algorithm is the only known quantum attack against symmetric key cryptography and, aside from this, there are no quantum algorithms to crack secret keys. Thus, many PKC schemes are at greater risk than SKC schemes. Most PKC schemes — including RSA — use some form of prime factorization. Even though PKC schemes typically use keys that are an order of magnitude larger than those of SKC methods, the prime factorization algorithms are susceptible to attack using Shor's algorithm, a quantum computing method for finding the prime factors of an integer.

The classic method to determine the prime factors of an integer is called the _general number field sieve_. The time required to factor an integer, N, with the field sieve algorithm rises exponentially, increasing as a function of _e_<sup>log N</sup>. (Euler's Number — known as _e_ — is an irrational number with a value 2.7182818284590452353....) Shor's algorithm, developed by Peter Shor in 1994, can solve the problem in polynomial time, or a value that is a function of log N.

> **NOTE:** Here's a quick word on _e_ and the log function. First, a review of logarithms. A base _x_ logarithmic value of a number is the power of _x_ that equals the number. So, for example, the _log_ function with no subscript implies a base of 10. Therefore, log 100 = 2 because 10<sup>2</sup>\=100, and log 1000 = 3. Similarly, log<sub>2</sub> 1024 = 10 because 2<sup>10</sup>\=1024 and log<sub>2</sub> 65,536 = 16. Note that when we say that some set of values increases exponentially, we mean that they rise as a power of some base, rather than linearly as the multiple of some base. The value _e_ (2.718281828459045...) is the basis for natural logarithms and many applications in mathematics; a _natural logarithm_ is merely log<sub>e</sub> (denoted _ln_). The exponentiation function applied to a number is the inverse of determining the logarithm of a number.
> 
> | x | _e_<sup>x</sup> | log x | _e_<sup>log x</sup> |
> | --- | --- | --- | --- |
> | 0 | 1 | undefined | undefined |
> | 1 | 2.7182818... | 0 | 1 |
> | 10 | ≈22026 | 1 | 2.7182818... |
> | 100 | ≈2.688117x10<sup>43</sup> | 2 | 7.3890561... |
> | 1000 | ≈1.970071x10<sup>434</sup> | 3 | 20.0855369... |

So, back to the compute time comparison between the general number field sieve and Shor's algorithm. As the table above shows, powers of _e_ grow quite rapidly while logarithms increase slowly. This has a clear impact on computational complexity. Problems that grow exponentially in terms of complexity eventually rise to a level such that no computer can actually provide a solution, even though the algorithm is known. Quantum computers can reduce the complexity of some of these problems so that they become computationally feasible. In terms of cryptography, if a quantum computer with a sufficient number of qubits could operate without succumbing to some of the problems of quantum computing (e.g., quantum noise and other quantum-decoherence phenomena), then Shor's algorithm could be used to break PKC schemes such as RSA, Diffie-Hellman key exchange, and Elliptic Curve Diffie-Hellman key exchange. It won't affect SKC schemes, such as AES, that are not based on prime factorization.

Again, there's a ton more that you can read about this topic; here are some starting points:

-   [Quantum Cryptography page](https://en.wikipedia.org/wiki/Quantum_cryptography) at Wikipedia
-   [Shor's Algorithm page](https://en.wikipedia.org/wiki/Shor's_algorithm) at Wikipedia
-   Bogomolec, X., Underhill, J.G., & Kovac, S.A. (2019). Towards Post-Quantum Secure Symmetric Cryptography: A Mathematical Perspective. _Cryptology ePrint Archive_, [Paper 2019/1208](https://eprint.iacr.org/2019/1208).
-   Hamdi, S.H., Zuhori, S.T., Mahmud, F., & Pal, B. (2014). A Compare Between Shor's Quantum Factoring Algorithm and General Number Field Sieve. In _Proceedings of the 2014 International Conference on Electrical Engineering and Information & Communication Technology_, April 10-12, 2014, pp. 1-6. DOI: [10.1109/ICEEICT.2014.6919115](https://doi.org/10.1109/ICEEICT.2014.6919115)
-   Schneier, B. (2018, September/October). Cryptography After the Aliens Land. _IEEE Security and Privacy_, _16_(5), 86-88. DOI: [10.1109/MSP.2018.3761724](https://doi.org/10.1109/MSP.2018.3761724) _**or**_ [https://www.schneier.com/essays/archives/2018/09/cryptography\_after\_t.html](https://www.schneier.com/essays/archives/2018/09/cryptography_after_t.html)

> **Let the games begin!!!** Bruce Schneier reports, in a January 2023 blog titled " [Breaking RSA with a Quantum Computer](https://www.schneier.com/blog/archives/2023/01/breaking-rsa-with-a-quantum-computer.html)," that a group of Chinese researchers claim to have a quantum computer design that can break 2048-bit RSA. It's an interesting read about what they claim what they _can_ do versus what is actually possible. And then there are a number of updates to the blog with responses by others saying that the claim is false. We're going to see a lot of this for the next few years.
> 
> Meanwhile, a team of Chinese researchers, led by Shanghai University, has demonstrated that [D-Wave's quantum annealing computers](https://www.thebrighterside.news/post/for-the-first-time-ever-researchers-crack-rsa-and-aes-data-encryption/) can crack RSA, AES, and other similar encryption methods. The report was published in the _Chinese Journal of Computers_, _47_(5), May 2024.
> 
> In 2019, researchers at the Google AI Quantum team published this in the abstract to a paper: "The promise of quantum computers is that certain computational tasks might be executed exponentially faster on a quantum processor than on a classical processor. A fundamental challenge is to build a high-fidelity processor capable of running quantum algorithms in an exponentially large computational space. Here we report the use of a processor with programmable superconducting qubits to create quantum states on 53 qubits, corresponding to a computational state-space of dimension 2<sup>53</sup> (about 10<sup>16</sup>)." (See: Arute, F., Arya, K., Babbush, R. et al. Quantum supremacy using a programmable superconducting processor. Nature _574_, 505-510 (2019). DOI: [https://doi.org/10.1038/s41586-019-1666-5](https://doi.org/10.1038/s41586-019-1666-5).) But quantum computers have, historically, had logical error rates that were too high to make the quantum computers practical. Maybe... until now? See: Google Quantum AI and Collaborators. Quantum error correction below the surface code threshold. _Nature_ (2024, December 9). DOI: [https://doi.org/10.1038/s41586-024-08449-y](https://doi.org/10.1038/s41586-024-08449-y).

The paragraphs above are preamble to an introduction to NIST's _post-quantum cryptographic standard (PQCS)_. In 2016, NIST started a competition to replace current PKC and digital signature algorithms with _quantum-resistant cryptography_, new methods that include "cryptographic algorithms or methods that are assessed not to be specifically vulnerable to attack by either a CRQC \[cryptanalytically relevant quantum computer\] or classical computer" ([Prepare for a New Cryptographic Standard to Protect Against Future Quantum-Based Threats](https://www.cisa.gov/uscert/ncas/current-activity/2022/07/05/prepare-new-cryptographic-standard-protect-against-future-quantum), July 5, 2022).

NIST has successfully used open competitions in the past, resulting in a new SKC standard (AES) in 2001 and a new hash function standard (SHA-3) in 2015. NIST received 82 PWCS proposals from around the world in 2017; 69 submissions made it through the Round 1 review, although five were subsequently withdrawn. In 2019, 26 submissions, including 17 public-key encryption and key-establishment Algorithms, and 9 digital signature algorithms, were announced as Round 2 finalists. Seven of the algorithms, including four public-key encryption and key-establishment algorithms, and three digital signature algorithms (plus eight alternate candidates) became Round 3 finalists in 2020. Finally, four public-key encryption and key-establishment algorithms — BIKE, Classic McEliece, HQC, and SIKE — became Round 4 finalists in July 2022.

Although the competition has been going on for several years, new cryptanalytic methods have been discovered that can be used against classic and post-quantum algorithms, and new weaknesses are being found in late-round algorithms.

-   One Round X algorithm, Rainbow, was found to be so broken that it could be cracked with an off-the-shelf laptop in about two days.
-   Three Round X finalists (Kyber, Saber, and Dilithium) were attacked with new methods that will likely be able to be employed against other PQCS algorithms.
-   In August 2022, Round 4 finalist SIKE was successfully attacked in one hour by a single-core PC, using a classic (rather than quantum-specific) attack.

So, the process continues and the future will be one of determining the viability of building quantum computers and the ability to build quantum cryptographic algorithms. As usual, a massive amount of information can be found in the Web, here are some places to start looking:

-   [Post-Quantum Cryptography Standardization page](https://csrc.nist.gov/Projects/post-quantum-cryptography/post-quantum-cryptography-standardization) at NIST
-   Cybersecurity and Infrastructure Security Agency (CISA). (2022, July 5). Prepare for a New Cryptographic Standard to Protect Against Future Quantum-Based Threats. [https://www.cisa.gov/uscert/ncas/current-activity/2022/07/05/prepare-new-cryptographic-standard-protect-against-future-quantum](https://www.cisa.gov/uscert/ncas/current-activity/2022/07/05/prepare-new-cryptographic-standard-protect-against-future-quantum)
-   Goodin, D. (2022, August). Post-Quantum Encryption Contender is Taken out by Single-Core PC and 1 Hour. _ARS Technica_. [https://arstechnica.com/information-technology/2022/08/sike-once-a-post-quantum-encryption-contender-is-koed-in-nist-smackdown/](https://arstechnica.com/information-technology/2022/08/sike-once-a-post-quantum-encryption-contender-is-koed-in-nist-smackdown/)
-   Schneier, B. (2022, August). NISTâ€™s Post-Quantum Cryptography Standards Competition. _Schneier on Security blog_. [https://www.schneier.com/blog/archives/2022/08/nists-post-quantum-cryptography-standards.html](https://www.schneier.com/blog/archives/2022/08/nists-post-quantum-cryptography-standards.html)

In August 2024, NIST released three encryption tools designed to withstand attack by a quantum computer. These post-quantum encryption standards are designed to be general-purpose, securing everything from e-mail to e-commerce. These new standards are:

-   Module-Lattice-Based Key-Encapsulation Mechanism Standard ([FIPS 203](https://doi.org/10.6028/NIST.FIPS.203))
-   Module-Lattice-Based Signature Standard ([FIPS 204](https://doi.org/10.6028/NIST.FIPS.204))
-   Stateless Standard Hash-Based Digital Signature ([FIPS 205](https://doi.org/10.6028/NIST.FIPS.205))

### CONCLUSION AND SOAP BOX

This paper has briefly (_well, it started out as "briefly"!_) described how digital cryptography works. The reader must beware, however, that there are a number of ways to attack every one of these systems; cryptanalysis and attacks on cryptosystems, however, are well beyond the scope of this paper. In the words of Sherlock Holmes (ok, Arthur Conan Doyle, really), "What one man can invent, another can discover" ("The Adventure of the Dancing Men," in: _The Return of Sherlock Holmes_, 1903).

There are a lot of topics that have been discussed above that will be big issues going forward in cryptography. As compute power increases, attackers can go after bigger keys and local devices can process more complex algorithms. Some of these issues include the size of public keys, the ability to forge public key certificates, which hash function(s) to use, and the trust that we will have in random number generators. Interested readers should check out "Recent Parables in Cryptography" (Orman, H., January/February 2014, _IEEE Internet Computing_, _18_(1), 82-86).

Cryptography is a particularly interesting field because of the amount of work that is, by necessity, done in secret. The irony is that secrecy is _not_ the key (no pun intended) to the goodness of a cryptographic algorithm. Regardless of the mathematical theory behind an algorithm, the best algorithms are those that are well-known and well-documented because they are also well-tested and well-studied! In fact, _time_ is the only true test of good cryptography; any cryptographic scheme that stays in use year after year is most likely a good one. The strength of cryptography lies in the choice (and management) of the keys; [longer keys will resist attack better than shorter keys](http://www.schneier.com/paper-keylength.html).

The corollary to this is that consumers should run, not walk, away from any product that uses a proprietary cryptography scheme, ostensibly because the algorithm's secrecy is an advantage. The observation that a cryptosystem should be secure even if everything about the system — except the key — is known by your adversary has been a fundamental tenet of cryptography for well over 125 years. It was first stated by Dutch linguist Auguste Kerckhoffs von Nieuwenhoff in his 1883 (yes, **18**83) papers titled [_La Cryptographie militaire_](http://petitcolas.net/fabien/kerckhoffs/), and has therefore become known as "[Kerckhoffs' Principle](https://en.wikipedia.org/wiki/Kerckhoffs's_principle)."

___

> **SIDEBAR:** Getting a new crypto scheme accepted, marketed, and, commercially viable is a hard problem particularly if someone wants to make money off of the invention of a "new, unbreakable" methodology. Many people want to sell their new algorithm and, therefore, don't want to expose the scheme to the public for fear that their idea will be stolen. I observe that, consistent with Kerckhoffs' Principle, this approach is doomed to fail. A company won't invest in a secret scheme because there's no need; one has to demonstrate that their algorithm is better and stronger than what is currently available before someone else will invest time and money to explore an unknown promise. Regardless, I would also suggest that the way to make money in crypto is in the packaging — how does the algorithm fit into user applications and how easy is it for users to use? Check out a wonderful paper about crypto usability, titled "[Why Johnny Can't Encrypt](https://www.usenix.org/legacy/events/sec99/full_papers/whitten/whitten_html/index.html)" (Whitten, A., & Tygar, J.D., 1999, _Proceedings of the 8th USENIX Security Symposium_, August 23-26, 1999, Washington, D.C., pp. 169-184.).
> 
> As a slight aside, another way that people try to prove that their new crypto scheme is a good one without revealing the mathematics behind it is to provide a public challenge where the author encrypts a message and promises to pay a sum of money to the first person — if any — who cracks the message. Ostensibly, if the message is not decoded, then the algorithm must be unbreakable. As an example, back in ~2011, a $10,000 challenge page for a new crypto scheme called DioCipher was posted and scheduled to expire on 1 January 2013 — which it did. That was the last that I heard of DioCipher. I leave it to the reader to consider the validity and usefulness of the public challenge process.

___

Finally, I can't ignore crypto snake oil or, to be fair, things that appear to be snake oil. The saga of Crown Sterling, a self-proclaimed "leading digital cryptography firm," is a case in point. Crown Sterling revealed a scheme called [TIME AI™](https://timeai.io/) — "the World's First 'Non-factor' Based Quantum Encryption Technology" — at BlackHat 2019. Sounds pretty cool but even a shallow analysis shows a lot of use of buzzwords, hand-waving, and excellent graphics... but no math, no algorithms, and no code. There's been a lot written about this and it is a good case study for the student of cryptography; for a short list, see:

-   Gallagher, S. (2019, September 20). Medicine show: Crown Sterling demos 256-bit RSA key-cracking at private event. _Ars Technica_. [https://arstechnica.com/information-technology/2019/09/medicine-show-crown-sterling-demos-256-bit-rsa-key-cracking-at-private-event/](https://arstechnica.com/information-technology/2019/09/medicine-show-crown-sterling-demos-256-bit-rsa-key-cracking-at-private-event/)
-   Schneier, B. (2019, September 5). The Doghouse: Crown Sterling. _Schneier on Security_. [https://www.schneier.com/blog/archives/2019/09/the\_doghouse\_cr\_1.html](https://www.schneier.com/blog/archives/2019/09/the_doghouse_cr_1.html)
-   Schneier, B. (2019, September 20). Crown Sterling Claims to Factor RSA Keylengths First Factored Twenty Years Ago. _Schneier on Security_. [https://www.schneier.com/blog/archives/2019/09/crown\_sterling\_.html](https://www.schneier.com/blog/archives/2019/09/crown_sterling_.html)
-   Without Bullshit. (2019, August 13). Is this Crown Sterling press release from another planet? [https://withoutbullshit.com/blog/is-this-crown-sterling-press-release-from-another-planet](https://withoutbullshit.com/blog/is-this-crown-sterling-press-release-from-another-planet)

### REFERENCES AND FURTHER READING

-   Bamford, J. (1983). _The Puzzle Palace: Inside the National Security Agency, America's most secret intelligence organization._ New York: Penguin Books.
-   Bamford, J. (2001). _Body of Secrets : Anatomy of the Ultra-Secret National Security Agency from the Cold War Through the Dawn of a New Century._ New York: Doubleday.
-   Barr, T.H. (2002). _Invitation to Cryptology._ Upper Saddle River, NJ: Prentice Hall.
-   Basin, D., Cremers, C., Miyazaki, K., Radomirovic, S., & Watanabe, D. (2015, May/June). Improving the Security of Cryptographic Protocol Standards. _IEEE Security & Privacy_, _13_(3), 24:31.
-   Bauer, F.L. (2002). _Decrypted Secrets: Methods and Maxims of Cryptology_, 2nd ed. New York: Springer Verlag.
-   Belfield, R. (2007). _The Six Unsolved Ciphers: Inside the Mysterious Codes That Have Confounded the World's Greatest Cryptographers._ Berkeley, CA: Ulysses Press.
-   Boneh, D., & Shoup, V. (2020, January). _A Graduate Course in Applied Cryptography_, v0.5. [https://toc.cryptobook.us/book.pdf](https://toc.cryptobook.us/book.pdf).
-   Denning, D.E. (1982). _Cryptography and Data Security._ Reading, MA: Addison-Wesley.
-   Diffie, W., & Landau, S. (1998). _Privacy on the Line._ Boston: MIT Press.
-   Electronic Frontier Foundation. (1998). _Cracking DES: Secrets of Encryption Research, Wiretap Politics & Chip Design_. Sebastopol, CA: O'Reilly & Associates.
-   Esslinger, B., & the CrypTool Team. (2017, January 21). _The CrypTool Book: Learning and Experiencing Cryptography with CrypTool and SageMath_, 12th ed. CrypTool Project. [https://www.cryptool.org/images/ctp/documents/CT-Book-en.pdf](https://www.cryptool.org/images/ctp/documents/CT-Book-en.pdf)
-   Federal Information Processing Standards (FIPS) 140-2. (2001, May 25). _Security Requirements for Cryptographic Modules._ Gaithersburg, MD: National Institute of Standards and Technology (NIST). [http://csrc.nist.gov/publications/fips/fips140-2/fips1402.pdf](http://csrc.nist.gov/publications/fips/fips140-2/fips1402.pdf)
-   Ferguson, N., & Schneier, B. (2003). _Practical Cryptography._ New York: John Wiley & Sons.
-   Ferguson, N., Schneier, B., & Kohno, T. (2010). _Cryptography Engineering: Design Principles and Practical Applications._ New York: John Wiley & Sons.
-   Flannery, S. with Flannery, D. (2001). _In Code: A Mathematical Journey._ New York: Workman Publishing Company.
-   Ford, W., & Baum, M.S. (2001). _Secure Electronic Commerce: Building the Infrastructure for Digital Signatures and Encryption,_ 2nd ed. Englewood Cliffs, NJ: Prentice Hall.
-   Garfinkel, S. (1995). _PGP: Pretty Good Privacy._ Sebastopol, CA: O'Reilly & Associates.
-   Grant, G.L. (1997). _Understanding Digital Signatures: Establishing Trust over the Internet and Other Networks._ New York: Computing McGraw-Hill.
-   Grabbe, J.O. (1997, October 10). Cryptography and Number Theory for Digital Cash. [http://www-swiss.ai.mit.edu/6.805/articles/money/cryptnum.htm](http://www-swiss.ai.mit.edu/6.805/articles/money/cryptnum.htm)
-   Haufler, H. (2003). _Codebreakers' Victory: How the Allied Cryptographers Won World War II_. New York: Open Road Integrated Media.
-   Kahn, D. (1983). _Kahn on Codes: Secrets of the New Cryptology._ New York: Macmillan.
-   Kahn, D. (1996). _The Codebreakers: The Comprehensive History of Secret Communication from Ancient Times to the Internet_, revised ed. New York: Scribner.
-   Katz, J., & Lindell, Y. (2021). _Introduction to Modern Cryptography_. Boca Raton, FL: Chapman & Hall/CRC Press, Taylor & Francis Group.
-   Kaufman, C., Perlman, R., & Speciner, M. (1995). _Network Security: Private Communication in a Public World_. Englewood Cliffs, NJ): Prentice Hall.
-   Koblitz, N. (1994). _A Course in Number Theory and Cryptography,_ 2nd ed. New York: Springer-Verlag.
-   Levy, S. (1999, April). The Open Secret. _WIRED Magazine_, _7_(4). [http://www.wired.com/wired/archive/7.04/crypto.html](http://www.wired.com/wired/archive/7.04/crypto.html)
-   Levy, S. (2001). _Crypto: How the Code Rebels Beat the Government — Saving Privacy in the Digital Age._ New York: Viking Press.
-   Mao, W. (2004). _Modern Cryptography: Theory & Practice_. Upper Saddle River, NJ: Prentice Hall Professional Technical Reference.
-   Marks, L. (1998). _Between Silk and Cyanide: A Codemaker's War, 1941-1945_. New York: The Free Press (Simon & Schuster).
-   National Academies of Sciences, Engineering, and Medicine. (2022). _Cryptography and the Intelligence Community: The Future of Encryption_. Washington, DC: The National Academies Press. DOI: [10.17226/26168](https://doi.org/10.17226/26168).
-   Schneier, B. (1996). _Applied Cryptography_, 2nd ed. New York: John Wiley & Sons.
-   Schneier, B. (2000). _Secrets & Lies: Digital Security in a Networked World_. New York: John Wiley & Sons.
-   Simion, E. (2015, January/February). The Relevance of Statistical Tests in Cryptography. _IEEE Security & Privacy_, _13_(1), 66:70.
-   Singh, S. (1999). _The Code Book: The Evolution of Secrecy from Mary Queen of Scots to Quantum Cryptography._ New York: Doubleday.
-   Smart, N. (2014). _Cryptography: An Introduction_, 3rd ed. [https://www.cs.umd.edu/~waa/414-F11/IntroToCrypto.pdf](https://www.cs.umd.edu/~waa/414-F11/IntroToCrypto.pdf)
-   Smith, L.D. (1943). _Cryptography: The Science of Secret Writing._ New York: Dover Publications.
-   Spillman, R.J. (2005). _Classical and Contemporary Cryptology_. Upper Saddle River, NJ: Pearson Prentice-Hall.
-   Stallings, W. (2006). _Cryptography and Network Security: Principles and Practice_, 4th ed. Englewood Cliffs, NJ: Prentice Hall.
-   Young, A., & Yung, M. (2004). _Malicious Cryptography: Exposing Cryptovirology_. New York: John Wiley & Sons.
  
-   On the Web:
    
    -   [Bob Lord's Online Crypto Museum](https://www.ilord.com/)
    -   [Crypto Museum](http://cryptomuseum.com/)
    -   [_Crypto-Gram_ Newsletter](https://www.schneier.com/crypto-gram.html)
    -   [Cypherpunk -- A history](https://en.wikipedia.org/wiki/Cypherpunk)
    -   [Internet Engineering Task Force (IETF) Security Area](http://trac.tools.ietf.org/area/sec/trac/wiki)
    
    -   [An Open Specification for Pretty Good Privacy (openpgp)](https://datatracker.ietf.org/wg/openpgp/charter/)
    -   [Common Authentication Technology (cat)](https://datatracker.ietf.org/wg/cat/charter/)
    -   [IP Security Protocol (ipsec)](https://datatracker.ietf.org/wg/ipsec/charter/)
    -   [One Time Password Authentication (otp)](https://datatracker.ietf.org/wg/otp/charter/)
    -   [Public Key Infrastructure (X.509) (pkix)](https://datatracker.ietf.org/wg/pkix/charter/)
    -   [S/MIME Mail Security (smime)](https://datatracker.ietf.org/wg/smime/charter/)
    -   [Simple Public Key Infrastructure (spki)](https://datatracker.ietf.org/wg/spki/charter/)
    -   [Transport Layer Security (tls)](https://datatracker.ietf.org/wg/tls/charter/)
    -   [Web Transaction Security (wts)](https://datatracker.ietf.org/wg/wts/charter/)
    -   [Web Security (websec)](https://datatracker.ietf.org/wg/websec/charter/)
    -   [XML Digital Signatures (xmldsig)](https://datatracker.ietf.org/wg/xmldsig/charter/)
    
    -   [Kerberos: The Network Authentication Protocol](http://web.mit.edu/kerberos/) (MIT)
    -   [The MIT Kerberos & Internet trust (MIT-KIT) Consortium](http://www.kerberos.org/) (MIT)
    -   [Peter Gutman's godzilla crypto tutorial](http://www.cs.auckland.ac.nz/~pgut001/tutorial/index.html)
    -   [RSA's Cryptography FAQ](http://www.emc.com/emc-plus/rsa-labs/historical/crypto-faq.htm) (v4.1, 2000)
    -   Interspersed in RSA's [Public Key Cryptography Standards (PKCS)](http://www.emc.com/emc-plus/rsa-labs/standards-initiatives/public%20key-cryptography-standards.htm) pages are a very good set of chapters about cryptography.
    -   [Ron Rivest's "Cryptography and Security" Page](http://theory.lcs.mit.edu/~rivest/crypto-security.html)
    -   ["List of Cryptographers" from U.C. Berkeley](http://www.cs.berkeley.edu/~daw/people/crypto.html)
    
      
    
-   Software:
    -   [Wei Dai's Crypto++, a free C++ class library of cryptographic primitives](http://www.cryptopp.com/)
    -   [Peter Gutman's cryptlib security toolkit](http://www.cs.auckland.ac.nz/~pgut001/cryptlib/index.html)
    -   A Perl implementation of RC4 (for academic but not production purposes) can be found at [_http://www.garykessler.net/software/index.html#RC4_](https://www.garykessler.net/software/index.html#RC4).
    -   A Perl program to decode Cisco type 7 passwords can be found at [_https://www.garykessler.net/software/index.html#cisco7_](https://www.garykessler.net/software/index.html#cisco7).

And for a purely enjoyable fiction book that combines cryptography and history, check out Neal Stephenson's _[Cryptonomicon](https://www.harpercollins.com/9780060512804/cryptonomicon/)_ (published May 1999). You will also find in it a new secure crypto scheme based upon an ordinary deck of cards (ok, you need the jokers...) called the [Solitaire Encryption Algorithm](http://www.schneier.com/solitaire.html), developed by Bruce Schneier.

<table><tbody><tr><td><img src="https://www.garykessler.net/library/images/crypto_2600des.gif"></td><td><p>Finally, I am not in the clothing business although I did used to have an impressive t-shirt collection (over 350 and counting, at one point!). I still proudly wear the DES (well, actually the IDEA) encryption algorithm t-shirt from <i>2600 Magazine</i> which, sadly, appears to be no longer available (left). (It was always ironic to me that <i>The Hacker Quarterly</i> got the algorithm wrong but...) A t-shirt with Adam Back's RSA Perl code can be found at <a href="http://www.cypherspace.org/~adam/uk-shirt.html" target="_blank"><i>http://www.cypherspace.org/~adam/uk-shirt.html</i></a> (right).</p></td><td><img src="https://www.garykessler.net/library/images/crypto_backrsa.jpg" target="_blank"></td></tr></tbody></table>

### APPENDIX A. SOME MATH NOTES

A number of readers over time have asked for some rudimentary background on a few of the less well-known mathematical functions mentioned in this paper. Although this is purposely **not** a mathematical treatise, some of the math functions mentioned here are essential to grasping how modern crypto functions work. To that end, some of the mathematical functions mentioned in this paper are defined in greater detail below.

#### The Boolean Logic Functions

[George Boole](https://en.wikipedia.org/wiki/George_Boole), a mathematician and philosopher in the mid-1800s, invented a form of algebra with which to express logical arguments. [Boolean algebra](https://en.wikipedia.org/wiki/Boolean_algebra) provides the basis for building electronic computers and microprocessor chips. Boole defined a bunch of primitive logical operations where there are one or two inputs and a single output depending upon the operation; the input and output are either TRUE or FALSE. The most elemental Boolean operations are:

-   NOT (¬): The output value is the inverse of the input value (i.e., the output is TRUE if the input is false, FALSE if the input is true).
-   AND (• _or_ ∧): The output is TRUE if all inputs are true, otherwise FALSE. (E.g., "the sky is blue AND the world is flat" is FALSE while "the sky is blue AND security is a process" is TRUE.)
-   OR (+ _or_ ∨): The output is TRUE if either or both inputs are true, otherwise FALSE. (E.g., "the sky is blue OR the world is flat" is TRUE and "the sky is blue OR security is a process" is TRUE.)
-   Exclusive OR (XOR, ⊕): The output is TRUE if exactly one of the inputs is TRUE, otherwise FALSE. (E.g., "the sky is blue XOR the world is flat" is TRUE while "the sky is blue XOR security is a process" is FALSE.)

The four Boolean functions are generally represented by the use of a _truth tables_; in computers, Boolean logic is implemented in _logic gates_. For design purposes, NOT has one input and the other three functions have two inputs (black); all have a single output (red). The logic diagrams appear below:

<table><tbody><tr><td><table><tbody><tr><td rowspan="2" colspan="2"><span color="red">¬</span></td><td colspan="2">Input</td></tr><tr><td><b>0</b></td><td><b>1</b></td></tr><tr><td colspan="2">Output</td><td><span color="red"><b>1</b></span></td><td><span color="red"><b>0</b></span></td></tr></tbody></table></td><td><table><tbody><tr><td rowspan="2" colspan="2"><span color="red">•</span></td><td colspan="2">Input #1</td></tr><tr><td><b>0</b></td><td><b>1</b></td></tr><tr><td rowspan="2">Input #2</td><td><b>0</b></td><td><span color="red"><b>0</b></span></td><td><span color="red"><b>0</b></span></td></tr><tr><td><b>1</b></td><td><span color="red"><b>0</b></span></td><td><span color="red"><b>1</b></span></td></tr></tbody></table></td><td><table><tbody><tr><td rowspan="2" colspan="2"><span color="red">+</span></td><td colspan="2">Input #1</td></tr><tr><td><b>0</b></td><td><b>1</b></td></tr><tr><td rowspan="2">Input #2</td><td><b>0</b></td><td><span color="red"><b>0</b></span></td><td><span color="red"><b>1</b></span></td></tr><tr><td><b>1</b></td><td><span color="red"><b>1</b></span></td><td><span color="red"><b>1</b></span></td></tr></tbody></table></td><td><table><tbody><tr><td rowspan="2" colspan="2"><span color="red"><b>⊕</b></span></td><td colspan="2">Input #1</td></tr><tr><td><b>0</b></td><td><b>1</b></td></tr><tr><td rowspan="2">Input #2</td><td><b>0</b></td><td><span color="red"><b>0</b></span></td><td><span color="red"><b>1</b></span></td></tr><tr><td><b>1</b></td><td><span color="red"><b>1</b></span></td><td><span color="red"><b>0</b></span></td></tr></tbody></table></td></tr></tbody></table>

XOR is one of the fundamental mathematical operations used in cryptography (and many other applications). In an XOR operation, the output will be a 1 if exactly one input is a 1; otherwise, the output is 0. The real significance of this is to look at the "identity properties" of XOR. In particular, any value XORed with itself is 0 (i.e., x ⊕ x = 0) and any value XORed with 0 is just itself (i.e., x ⊕ 0 = x). Why does this matter? Well, if you take a plaintext string of 0s and 1s, and XOR it with a key string of 0s and 1s, you get a new jumbled string of 0s and 1s. If you then take that jumble and XOR it with the same key, you return to the original plaintext.

**NOTE:** Boolean truth tables usually show the inputs and output as a single bit because they are based on single bit inputs, namely, TRUE and FALSE. In addition, we tend to apply Boolean operations bit-by-bit. For convenience, I have created [Boolean logic tables when operating on bytes](https://www.garykessler.net/library/byte_logic_table.html).

#### The _modulo_ Function

The _modulo_ function is, simply, the remainder function. It is commonly used in programming and is critical to the operation of any mathematical function using digital computers.

To calculate _X modulo Y_ (usually written _X mod Y_), you merely determine the remainder after removing all multiples of Y from X. Clearly, the value X mod Y will be in the range from 0 to Y-1.

Some examples should clear up any remaining confusion:

-   15 mod 7 = 1
-   25 mod 5 = 0
-   33 mod 12 = 9
-   203 mod 256 = 203

Modulo arithmetic is useful in crypto because it allows us to set the size of an operation and be sure that we will never get numbers that are too large. This is an important consideration when using digital computers.

#### Information Theory and Entropy

_Information theory_ is the formal study of reliable transmission of information in the least amount of space or, in the vernacular of information theory, the fewest _symbols_. For purposes of digital communication, a symbol can be a byte (i.e., an eight-bit octet) or an even smaller unit of transmission.

The father of information theory is Bell Labs scientist and MIT professor [Claude E. Shannon](https://en.wikipedia.org/wiki/Claude_E._Shannon). His seminal paper, "[A Mathematical Theory of Communication](http://www.math.harvard.edu/~ctm/home/text/others/shannon/entropy/entropy.pdf)" (_The Bell System Technical Journal_, Vol. 27, pp. 379-423, 623-656, July, October, 1948), defined a field that has laid the mathematical foundation for so many things that we take for granted today, from data compression, data storage, data communication, and quantum computing to language processing, plagiarism detection and other linguistic analysis, and statistical modeling. Information theory also applies to cryptography, and a student of information theory and cryptography will most certainly want to see the International Association for Cryptologic Research's page on Shannon's 1945 paper, "[A Mathematical Theory of Cryptography](https://www.iacr.org/museum/shannon45.html)."

There are many everyday computer and communications applications that have been enabled by the formalization of information theory, such as:

-   _Lossless data compression_, where the compressed data is an exact replication of the uncompressed source (e.g., PKZip, GIF, PNG, and WAV).
-   _Lossy data compression_, where the compressed data can be used to reproduce the original uncompressed source within a certain threshold of accuracy (e.g., JPG and MP3).
-   _Coding theory_, which describes the [impact of bandwidth and noise on the capacity of data communication channels](http://web.mit.edu/newsoffice/2010/explained-shannon-0115.html) from modems to Digital Subscriber Line (DSL) services, why a CD or DVD with scratches on the surface can still be read, and codes used in error-correcting memory chips and forward error-correcting satellite communication systems.

One of the key concepts of information theory is that of _entropy_. In physics, entropy is a quantification of the disorder in a system; in information theory, entropy describes the uncertainty of a random variable or the randomness of an information symbol. As an example, consider a file that has been compressed using PKZip. The original file and the compressed file have the same information content but the smaller (i.e., compressed) file has more entropy because the content is stored in a smaller space (i.e., with fewer symbols) and each data unit has more randomness than in the uncompressed version. In fact, a perfect compression algorithm would result in compressed files with the maximum possible entropy; i.e., the files would contain the same number of 0s and 1s, and they would be distributed within the file in a totally unpredictable, random fashion.

As another example, consider the entropy of passwords (this text is taken from my paper, "[Passwords — Strengths And Weaknesses](https://www.garykessler.net/library/password.html)," citing an example from _Firewalls and Internet Security: Repelling the Wily Hacker_ by Cheswick & Bellovin \[1994\]):

> Most Unix systems limit passwords to eight characters in length, or 64 bits. But Unix only uses the seven significant bits of each character as the encryption key, reducing the key size to 56 bits. But even this is not as good as it might appear because the 128 possible combinations of seven bits per character are not equally likely; users usually do not use control characters or non-alphanumeric characters in their passwords. In fact, most users only use lowercase letters in their passwords (and some password systems are case-insensitive, in any case). The bottom line is that ordinary English text of 8 letters has an information content of about 2.3 bits per letter, yielding an 18.4-bit key length for an 8-letter passwords composed of English words. Many people choose names as a password and this yields an even lower information content of about 7.8 bits for the entire 8-letter name. As phrases get longer, each letter only adds about 1.2 to 1.5 bits of information, meaning that a 16-letter password using words from an English phrase only yields a 19- to 24-bit key, not nearly what we might otherwise expect.

Encrypted files tend to have a great deal of randomness. This is why a compressed file can be encrypted but an encrypted file cannot be compressed; compression algorithms rely on redundancy and repetitive patterns in the source file and such syndromes do not appear in encrypted files.

Randomness is such an integral characteristic of encrypted files that an entropy test is often the basis for searching for encrypted files. Not all highly randomized files are encrypted, but the more random the contents of a file, the more likely that the file is encrypted. As an example, AccessData's Forensic Toolkit (FTK), software widely used in the computer forensics field, uses the following tests to detect encrypted files:

-   _Χ<sup>2</sup> Error Percent:_ This distribution is calculated for a byte stream in a file; the value indicates how frequently a truly random number would exceed the calculated value.
-   _Entropy:_ Describes the information density (per Shannon) of a file in bits/character; as entropy approaches 8, there is more randomness.
-   _MCPI Error Percent:_ The Monte Carlo algorithm uses statistical techniques to approximate the value of π; a high error rate implies more randomness.
-   _Serial Correlation Coefficient:_ Indicates the amount to which each byte is an e-mail relies on the previous byte. A value close to 0 indicates randomness.

___

> **SIDEBAR:** An 8-bit byte has 256 possible values. The entropy (E) of a binary file can be calculated using the following formula:  
> ![](https://www.garykessler.net/library/images/crypto_math_entropy.png) 
> 
> where n=256 and P(_x_<sub>i</sub>) is the probability of a byte in this file having the value _i_. A small Perl program to compute the entropy of a file can be found at [entropy.zip](https://www.garykessler.net/library/crypto/entropy.zip).

___

Given this need for randomness, how do we ensure that crypto algorithms produce random numbers for high levels of entropy? Computers use random number generators (RNGs) for myriad purposes but computers cannot actually generate truly random sequences but, rather, sequences that have mostly random characteristics. To this end, computers use [_pseudorandom number generator (PRNG)_](https://en.wikipedia.org/wiki/Pseudorandom_number_generator), aka _deterministic random number generator_, algorithms. NIST has a series of documents (SP 800-90: Random Bit Generators) that address this very issue:

-   [_SP 800-90A: Recommendation for Random Number Generation Using Deterministic Random Bit Generators_](http://csrc.nist.gov/publications/nistpubs/800-90A/SP800-90A.pdf)
-   [_Draft SP 800-90 B: Recommendation for the Entropy Sources Used for Random Bit Generation_](http://csrc.nist.gov/publications/drafts/800-90/draft-sp800-90b.pdf)
-   [_Draft SP 800-90 C: Recommendation for Random Bit Generator (RBG) Constructions_](http://csrc.nist.gov/publications/drafts/800-90/draft-sp800-90c.pdf)

The IETF's view of the randomness requirements for security can be found in [RFC 4086](https://www.rfc-editor.org/info/rfc4086). That paper notes several pitfalls when weak forms of entropy or traditional PRNG techniques are employed for purposes of security and cryptography. They also recommend the use of truly random hardware techniques and describe how existing systems can be used for these purposes. [RFC 8937](https://www.rfc-editor.org/info/rfc8937), produced by the [Crypto Forum Research Group (CFRG)](https://irtf.org/cfrg), describes how security protocols can supplement their so-called "cryptographically secure" PRNG algorithms using long-term private keys.

___

> **SIDEBAR:** While the purpose of this document is supposed to be tutorial in nature, I cannot totally ignore the disclosures of [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) in 2013 about NSA activities related to cryptography. One interesting set of disclosures is around deliberate weaknesses in the NIST PRNG standards at the behest of the NSA. NIST denies any such purposeful flaws but this will be evolving news over time. Interested readers might want to review ["NSA encryption backdoor proof of concept published"](http://www.zdnet.com/nsa-encryption-backdoor-proof-of-concept-published-7000024793/) (M. Lee), ["Dual\_EC\_DRBG backdoor: a proof of concept"](http://blog.0xbadc0de.be/archives/155) (A. Adamantiadis), or ["Juniper Breach Mystery Starts to Clear With New Details on Hackers and U.S. Role"](https://www.bloomberg.com/news/features/2021-09-02/juniper-mystery-attacks-traced-to-pentagon-role-and-chinese-hackers) (J. Robertson).
> 
> Before thinking that this is too obscure to worry about, let me point out a field of study called _kleptography_, the "study of stealing information securely and subliminally" (see ["The Dark Side of Cryptography: Kleptography in Black-Box Implementations"](https://www.infosecurity-magazine.com/magazine-features/the-dark-side-of-cryptography-kleptography-in/)). Basically, this is a form of attack from within a cryptosystem itself. From that article comes this whimsical example: Imagine a cryptosystem (hardware or software) that generates PKC key pairs. The private key should remain exclusively within the system in order to prevent improper use and duplication. The public key, however, should be able to be freely and widely distributed since the private key cannot be derived from the public key, as described elsewhere in this document. But, now suppose that a cryptographic back door is embedded into the cryptosystem, allowing an attacker to access or derive the private key from the public key — such as weakening the key generation process at its heart by compromising the random number generators essential to creating strong key pairs. The potential negative impact is obvious.
> 
> Along these lines, another perspective of the Snowden disclosures relates to the impact on the world's most confidential data and critical infrastructures if governments are able to access encrypted communications. In July 2015, 14 esteemed cryptographers and computer scientists released a paper continuing the debate around cryptography and privacy. The paper, titled ["Keys Under Doormats: Mandating insecurity by requiring government access to all data and communications,"](http://dspace.mit.edu/bitstream/handle/1721.1/97690/MIT-CSAIL-TR-2015-026.pdf) argues that government access to individual users' encrypted information will ultimately yield significant flaws in larger systems and infrastructures. Also check out the _N.Y. Times_ article, ["Security Experts Oppose Government Access to Encrypted Communication"](http://www.nytimes.com/2015/07/08/technology/code-specialists-oppose-us-and-british-government-access-to-encrypted-communication.html) (N. Perlroth).

___

Do not underestimate the importance of good random number generation to secure cryptography — and do not forget that an algorithm might be excellent but the implementation poor. Read, for example, ["Millions of high-security crypto keys crippled by newly discovered flaw"](https://arstechnica.com/information-technology/2017/10/crypto-failure-cripples-millions-of-high-security-keys-750k-estonian-ids/) (D. Goodin), which reported on a weakness in an RSA software library. Because RSA prime factorization arithmetic can be very complex on smart cards and other energy and memory constrained devices, the code for generating keys employed coding shortcuts. The result was that an attacker could calculate the private key from a vulnerable key-pair by only knowing the public key, which is totally anathema to the whole concept of public-key cryptography (i.e., the public key is supposed to be widely known without compromise of the private key). The vulnerability was due to the fact that the weakness was in the RNG and, therefore, a reduced level of randomness in the relationship between the private and public keys. This flaw, exposed in November 2017, had been present since at least 2012.

For readers interested in learning more about information theory, see the following sites:

-   [Wikipedia entry for _Information Theory_](https://en.wikipedia.org/wiki/Information_theory)
-   [A Short Course in Information Theory](http://www.inference.phy.cam.ac.uk/mackay/info-theory/course.html) (Eight lectures by David J.C. MacKay)
-   [_Entropy and Information Theory_](http://ee.stanford.edu/~gray/it.pdf) by Gray (Revised 1st ed., 1991). In 2011, the [second edition](http://www.springer.com/engineering/signals/book/978-1-4419-7969-8) was published.

Finally, it is important to note that information theory is a continually evolving field. There is an area of research essentially questioning the "power" of entropy in determining the strength of a cryptosystem. An interesting paper about this is ["Brute force searching, the typical set and Guesswork"](http://arxiv.org/pdf/1301.6356.pdf) by Christiansen, Duffy, du Pin Calmon, & Médard (2013 IEEE International Symposium on Information Theory); a relatively non-technical overview of that paper can be found at ["Encryption is less secure than we hoped"](https://www.networkworld.com/article/2225195/encryption-is-less-secure-than-we-hoped.html) by Gibbs (_NetworkWorld_, 08/16/2013). If you're going this far, also take a look at "Entropy as a Service: Unlocking Cryptography's Full Potential" (A. Vassilev & R. Staples, September 2016, _Computer_, _49_(9), pp. 98-102).

#### Cryptography in the Pre-Computer Era

This paper purposely focuses on cryptography terms, concepts, and schemes used in digital devices and is not a treatise of the whole field. No mention is made here about pre-computerized crypto schemes, the difference between a substitution and transposition cipher, cryptanalysis, or other history, nor is there a lot of theory presented here. That said, the history and evolution of cryptography is really interesting and readers should check out some of the books in the [References and Further Reading section](https://www.garykessler.net/library/crypto.html?continueFlag=5df56630de56adc0f34bbcb2d0d23dd1#refs) above.

There are also several excellent Web sites that provide detailed information about the history of cryptographic methods, such as:

-   [Crypto Lab](http://manansingh.github.io/Cryptolab-Offline/) is a series of blog-like posts describing various encryption methods. The entire set of pages for offline viewing can be found at [GitHub](https://github.com/mananSingh/Cryptolab-Offline/archive/gh-pages.zip).
    
-   [Crypto Programs](http://www.cryptoprograms.com/home) is an online collection of more than 50 classical substitution and transposition encryption schemes.
    
-   The [CrypTool Portal](https://www.cryptool.org/en/) is designed to raise awareness about cryptography and contains a collection of free software with which to experiment with a variety of ciphers.
    
-   The [Learn Cryptography](https://learncryptography.com/encryption) _Encryption_ page has a lot of information about classical and historic encryption methods, as well as pages about cryptanalysis, cryptocurrency, hash functions, and more.
    
-   The [MultiWingSpan](http://www.multiwingspan.co.uk/cipher.php) _Ciphers_ page discusses a dozen or so manual encryption schemes as a setup to a series of programming assignments.
    
-   The [Practical Cryptography](http://practicalcryptography.com/ciphers/) _Ciphers_ page provides history, math, and sample implementations of a couple dozen manual encryption codes. Other pages discuss cryptanalysis and hash functions.
    

Finally, my software page contains a Perl program that implements several [manual crypto](https://www.garykessler.net/software/index.html#manual_crypto) schemes, including Caesar, Atbash, Vigenere Square, Beaufort, Myszkowski Transpostition, and ADFGVX ciphers.

#### A Short Introduction to Groups

Without a lot of explanation, I have made passing reference a few times in this paper to the concept of a _group_. In this section, I will give a very light introduction to some group theory. In mathematics, a _group_ refers to a set of elements that can be combined by some operation. An important requirement is that when any two elements within the group are combined using the operation, the result is another element that is a member of the group. In that case, the group is said to be _closed_ under that operation.

So, by way of example, the set of positive integers ({1...∞}) is closed under addition because any two members of this group, when added, yields another member of the group. As a simple example, 3 and 18 are both members of the set of positive integers, as is their sum, 21. The sets of non-negative integers ({0...∞}) and all integers ({-∞...∞}, often denoted **Z**) are also closed under addition.

These three groups are also closed under multiplication because, again, if you start with any two members of the set, their product is also a member of the set (e.g., 3 × 18 = 54).

As it happens, these three groups are not all closed under subtraction. Consider that 3 - 18 = -15. This shows that **Z** is closed under subtraction, but the subsets of positive and non-negative integers are not.

Finally, none of these sets are closed under division. Continuing with our friends, 3 and 18, we find that 3 ÷ 18 = 1/6 which is not a member of any set or subset of integers.

So, continuing down this path just a bit more, let's take a look at the set of rational numbers. _Rational numbers_, usually denoted **Q**, are numbers that can be expressed as a fraction _p/q_, where _p_ and _q_ are integers and _q_≠0. The set of rational numbers is closed under division. As an example, consider two rational numbers, 5/6 and 11/12; dividing (5/6)/(11/12) yields a new rational number, namely 60/66 which can be reduced to 10/11. _Irrational numbers_, such as _e_, π, and √2, cannot be expressed as a simple fraction.

### APPENDIX B. ACRONYMS AND ABBREVIATIONS

<table><tbody><tr><td>3DES</td><td>Triple DES</td></tr><tr><td>AES</td><td>Advanced Encryption Standard</td></tr><tr><td>AH</td><td>IPsec Authentication Header</td></tr><tr><td>ANSI</td><td>American National Standards Institute</td></tr><tr><td>ARPANET</td><td>Advanced Research Projects Agency</td></tr><tr><td>ASCII</td><td>American Standard Code for Information Interchange</td></tr><tr><td>ASIC</td><td>Application-specific integrated circuit</td></tr><tr><td>CA</td><td>Certificate authority</td></tr><tr><td>CBC</td><td>Cipher block chaining</td></tr><tr><td>CHAP</td><td>Challenge-Handshake Authentication Protocol</td></tr><tr><td>CPU</td><td>Central processing unit</td></tr><tr><td>CRYPTEC</td><td>Cryptography Research and Evaluation Committees</td></tr><tr><td>DES</td><td>Data Encryption Standard</td></tr><tr><td>DSA</td><td>Digital Signature Algorithm</td></tr><tr><td>DSS</td><td>Digital Signature Standard</td></tr><tr><td>ECC</td><td>Elliptical Curve Cryptography</td></tr><tr><td>EFF</td><td>Electronic Frontier Foundation</td></tr><tr><td>EFS</td><td>Encrypting File System</td></tr><tr><td>EPIC</td><td>Electronic Privacy Information Center</td></tr><tr><td>ESP</td><td>IPsec Encapsulating Security Payload</td></tr><tr><td>FIPS</td><td>Federal Information Processing Standard</td></tr><tr><td>FPGA</td><td>Field-programmable gate array</td></tr><tr><td>FTP</td><td>File Transfer Protocol</td></tr><tr><td>GCHQ</td><td>Government Communications Headquarters</td></tr><tr><td>GPG</td><td>GNU Privacy Guard</td></tr><tr><td>GUI</td><td>Graphical user interface</td></tr><tr><td>HMAC</td><td>Hash-based <i>or</i> Keyed-Hash Message Authentication Code</td></tr><tr><td>HTTP</td><td>Hypertext Transfer Protocol</td></tr><tr><td>HTTPS</td><td>Hypertext Transfer Protocol over SSL</td></tr><tr><td>IBE</td><td>Identify-Based Encryption</td></tr><tr><td>IDEA</td><td>International Data Encryption Algorithm</td></tr><tr><td>IEEE</td><td>Institute of Electrical and Electronics Engineers</td></tr><tr><td>IETF</td><td>Internet Engineering Task Force</td></tr><tr><td>IKE</td><td>IPsec Internet Key Exchange</td></tr><tr><td>IP</td><td>Internet Protocol <i>or</i> Initial permutation</td></tr><tr><td>IPsec</td><td>IP Security</td></tr><tr><td>IPv4/IPv6</td><td>Internet Protocol version 4/version 6</td></tr><tr><td>ISAKMP</td><td>IPsec Internet Security Association and Key Management Protocol</td></tr><tr><td>KB</td><td>kilobyte; 1,024 (2<sup>10</sup>) bytes</td></tr><tr><td>MAC</td><td>Message Authentication Code</td></tr><tr><td>MD<i>n</i></td><td>Message Digest <i>n</i> (MD2, MD4, MD5)</td></tr><tr><td>MIME</td><td>Multipurpose Internet Mail Extensions</td></tr><tr><td>NBS</td><td>National Bureau of Standards</td></tr><tr><td>NESSIE</td><td>New European Schemes for Signatures, Integrity and Encryption</td></tr><tr><td>NIST</td><td>National Institute of Standards and Technology</td></tr><tr><td>NSA</td><td>National Security Agency</td></tr><tr><td>PC</td><td>Personal computer</td></tr><tr><td>PCMCIA</td><td>Personal Computer Memory Card International Association</td></tr><tr><td>PDA</td><td>Personal digital assistant</td></tr><tr><td>PEM</td><td>Privacy-Enhanced Mail</td></tr><tr><td>PGP</td><td>Pretty Good Privacy</td></tr><tr><td>PKC</td><td>Public key cryptography</td></tr><tr><td>PKCS</td><td>Public Key Cryptography Standards</td></tr><tr><td>PKI</td><td>Public key infrastructure</td></tr><tr><td>RAM</td><td>Random-access memory</td></tr><tr><td>RC<i>n</i></td><td>Ron's Code <i>n</i> <i>or</i> Rivest Cipher <i>n</i> (RC2, RC4, RC5, RC6)</td></tr><tr><td>RFC</td><td>Request for Comments</td></tr><tr><td>RFID</td><td>Radio-frequency identification</td></tr><tr><td>RSA</td><td>Rivest, Shamir, Adleman</td></tr><tr><td>S/MIME</td><td>Secure Multipurpose Internet Mail Extensions</td></tr><tr><td>SA</td><td>IPsec Security Association</td></tr><tr><td>SCUBA</td><td>Self-contained underwater breathing apparatus</td></tr><tr><td>SETI</td><td>Search for extraterrestrial intelligence</td></tr><tr><td>SHA</td><td>Secure Hash Algorithm</td></tr><tr><td>SKC</td><td>Secret key cryptography</td></tr><tr><td>SSL</td><td>Secure Sockets Layer</td></tr><tr><td>TCP</td><td>Transmission Control Protocol</td></tr><tr><td>TLS</td><td>Transaction Layer Security</td></tr><tr><td>UDP</td><td>User Datagram Protocol</td></tr><tr><td>URL</td><td>Uniform Resource Locator</td></tr><tr><td>USB</td><td>Universal Serial Bus</td></tr><tr><td>XOR</td><td>Exclusive OR</td></tr></tbody></table>

[Gary C. Kessler](https://www.garykessler.net/gck.html), Ph.D., CCE, CISSP, is the president and janitor of [Gary Kessler Associates](https://www.garykessler.net/), a consulting, research, and training firm specializing in computer and network security (with a focus on maritime cybersecurity), computer forensics, and TCP/IP networking. He has written over 75 papers, articles, and book chapters for industry publications, is co-author of _ISDN_, 4th. edition (McGraw-Hill, 1998), a past editor-in-chief of the [_Journal of Digital Forensics, Security and Law_](http://www.jdfsl.org/), and co-author of [_Maritime Cybersecurity: A Guide for Leaders and Managers_](https://www.garykessler.net/MaritimeCybersecurityBook/index.html), 2/e (2022). Gary retired as Professor of Cybersecurity at Embry-Riddle Aeronautical University in Daytona Beach, Florida, and is an Adjunct Professor at Edith Cowan University in Perth, Western Australia. Gary was formerly an Associate Professor and Program Director of the M.S. in Information Assurance program at Norwich University in Northfield, Vermont, and a member of the Vermont Internet Crimes Against Children (ICAC) Task Force; he started the M.S. in Digital Investigation Management and undergraduate Computer & Digital Forensics programs at Champlain College in Burlington, Vermont. Gary's e-mail address is _[gck@garykessler.net](mailto:gck@garykessler.net)_ and his PGP public key can be found at _[https://www.garykessler.net/pubkey.html](https://www.garykessler.net/pubkey.html)_. Gary is also a SCUBA instructor and U.S. Coast Guard licensed captain.

### ACKNOWLEDGEMENTS

This section was introduced late in the life of this paper and so I apologize to any of you who have made helpful comments that remain unacknowledged. If you did make comments that I adopted — from catching typographical or factual errors to suggesting a new resource or topic — and I have failed to recognize you, please remind me!

Thanks are offered to Steve Bellovin, Sitaram Chamarty, DidiSoft, Bernhard Esslinger (and his students at the University of Siegen, Germany, contributors to the [CrypTool project](https://www.cryptool.org/en/)), William R. Godwin, Craig Heilman, Luveh Keraph, Robert Litts, Hugh Macdonald, Douglas P. McNutt, Marcin Olak, Josh Silman, Barry Steyn, and Miles Wolbe.
