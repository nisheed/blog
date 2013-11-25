---
layout: post
title:  "Internet Security now!"
date:   2013-11-24 18:42:44
categories: security
comments: true
tags: SSL crypto paranoid
---

Am I being watched on the internet? may be, because it is easy to eavesdrop on the unencrypted traffic flowing to and from my laptop. Hmm.. so what about the cryptographically secure SSL connections? the answer now is still a 'may be'. Right from the most vulnerable public WiFi's that we connect often, to highly secure connections, the eavesdrop can happen.

WTF? Does that mean there is no way to communicate securely over the net? Fortunately no, there are ways to communicate with perfect secrecy over the internet, provided you understand the threats and adopt the best practices to rule them out.

It is a common fallacy that the privacy is accomplished if there is 'https' at the beginning of the url. But a passive attack is quite possible if the SSL is not configured properly. Badly configured SSL parameters can really screw your application security.

By the way, Have you been paying attention to the name 'Edward Snowden' showing up all over the security blogs nowadays? Why is he a subject of discussion at the international level? What is PRISM?

Edward Snowden, who once was a contractor at NSA, has warned the internet community of a major technical flaw in the SSL implementations being exploited 'legally' by a surveillance program called PRISM (section 702 of FISA) run by NSA, which supposedly has been doing a passive cryptanalysis on the encrypted correspondence data collected and stored offline from the customers of the major internet & telephone service providers. How is a passive cryptanalysis is so easy? It is just a matter of grabbing the private key of the service provider (with a court order or warrant) to decrypt all your communication done through a RSA protected SSL connection. The sad news is that a major portion of the SSL implementations use RSA for both authentication and encryption.

I am trying to put together some of the relevant security recommendations to adopt in order to attain the maximum online privacy and security. Most of the points are often discussed in security blogs, security conferences, openssl documentation and RFCs.

Threat 1.
---------
Passive cryptanalysis. This taps the SSL communication from some where on the wire and cryptanalyses the encrypted data later. This is possible if the server's private key is known to the attacker by some means.

Use Ephemeral Diffie Hellman (DHE) algorithm for as the key exchanger instead of RSA. DHE assures perfect forward secrecy by using per-session key which is generated afresh for each session. So decryption is not possible if the permanent secret (certificate private key) is leaked. (This is what PRISM does).

Threat 2.
---------
BEAST and Lucky13. This is CBC vulnerability.

Use TLS 1.2 with AES GCM suits. But the GCM mode is new and it is an arduous job to get every security systems (both at the server and the client sides) upgraded; So instead use RC4 which is a stream cipher and hence faster and CBC/IV-free. But the bad news is that RC4 has got its own security problems (fixed string cipher entropy problem) when compared to block ciphers like AES and DSA, but that is less devastating than what CBC mode offers.

Threat 3.
---------
CRIME attack. A vulnerability exposed by TLS compression. Exposes the site cookies on side-channel attacks.

Disable TLS compression. Most of the applications like Nginx and Apache have directives to disable compression.

Threat 4.
---------
CA as single point of trust. The CA certificate's private key has it all.

Embrace certificate pinning through HPKP (HTTP Public Key Pinning Extension) or TACK (Trust Assertions for Certificate Keys, a TLS extension). The certificate pinning removes the dependency on the CA certificate after the initial authentication. Certificate then pins to the certificate server instead of CA. This prevents active/passive cryptanalysis.

Threat 5.
---------
Tracking. Most of the search engines and social websites do it. They know about your cyber habits better than yourself.

Keep anonymity while browsing. Try the onion router (tor) systems that provides user as well as service anonymity. Use openVPN solutions and anonymous chat applications that respect privacy.

Threat 6.
---------
Browser & OS. IE doesn't offer PFS (Perfect Forward Secrecy). Win2k8R2 supports PFS but with BEAST vulnerabilities (CBC). Then RC4 is supposed to be a solution to that, but it is not supported by Win2k8R2.

Choose a browser and operating system that are open source. A browser that can talk the latest SSL ciphers. A browser that will let you disable tracking and third party cookies. A browser that you know what you can analyze the code and understand how it works.

Checking the SSL ciphers

You can check the ciphers supported by an SSL site using the openssl tool.


```
exponent@~ > openssl s_client -connect yahoo.com:443
---
SSL handshake has read 1399 bytes and written 456 bytes
---
New, TLSv1/SSLv3, Cipher is AES256-SHA
Server public key is 2048 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
SSL-Session:
    Protocol  : TLSv1
    Cipher    : AES256-SHA
    Session-ID:
    Session-ID-ctx:
    Master-Key: CAB7722C5ED37D00ACB35E983337CD4BF4C0B466642 \
                C2B933AF52991CA4A299DF7820DE8E27A005D51393602265831D8
    Key-Arg   : None
    Start Time: 1375166699
    Timeout   : 300 (sec)
    Verify return code: 21 (unable to verify the first certificate)
---
```


Look for phrase "Cipher is". Below is a sample cipher supported and how to interpret it.

_**TLS_ECDHE_RSA_WITH_AES256_CBC_SHA**_

*TLS* : Transport Layer Security </br>
=> It is the transport layer protocol used. </br>
[other possible values: SSL]

*ECDHE* : Elliptic Curve Diffie Hellman Ephemeral </br>
=> Session key exchange algorithm </br>
[other possible values: RSA, DH, DHE]

*RSA* : Rivest, Adleman and Shamir </br>
=> PKI type of Certificate Type </br>
[other possible values: DSS]

*AES256* : Advanced Encryption Standard 256 (bit key). </br>
=> Algorithm used to encrypt the actual data </br>
[other possible values: RC4, 3DES, DES]

*CBC* : Cypher Block Chaining </br>
=> Mode in which the cipher algorithm works. CBC is difficult to crack, but susceptible to BEAST/Luck13 attacks.) </br>
[other possible values: CBC3]

*SHA* : Secure Hash Algorithm</br>
=> A hashing algorithm for data integrity </br>
[other possible values: MD5] </br>

\-The End

***
<br />
<br />

<div id="disqus_thread"></div>
<script type="text/javascript">
    /* * * CONFIGURATION VARIABLES: EDIT BEFORE PASTING INTO YOUR WEBPAGE * * */
    var disqus_shortname = 'nisheedcom'; // required: replace example with your forum shortname

    /* * * DON'T EDIT BELOW THIS LINE * * */
    (function() {
        var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
        dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="http://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
<a href="http://disqus.com" class="dsq-brlink">comments powered by <span class="logo-disqus">Disqus</span></a>
