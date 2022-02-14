
# How to use the same keys to issue DIVOC, SmartHealthCard and EU-DCC

Both DIVOC and SmartHealthCard use a plain (i.e. there is no PKI or X.509 structure, just a raw public/private keypair) RSA or ECDSA keypair to sign the certificate. The EU system also uses the same cryptographic keys however it requires that they be distributed using PKi.

The challenge here is to mesh these plain keys with the PKI based approach of the EU-DCC. This document sets out the process for this.

## EU Signing Certificate Structure

In the DCC key policy a country has two x509 certificates: a Country Signing Certificate Authority (`CSCA`) and a Document Signing Certificate (`DSC`). The `CSCA` is a certificate which has been issued by a competent authority - for example a country's own CA or a respected commercial CA. The DSC is, in turn, issued by the `CSCA` certificate.

Here's a visualisation of the relationship between the two certificates:

	--------               ---------
	|      |               |       |
	| CSCA | ------------> |  DSC  |
	|      |    issues     |       |
	--------               ---------

There are rules agreed by https://sogis.eu/ regarding the handling of certificates. These must be followed by all DCC Member States For example the `CSCA` must be air-gapped - it must never be used on a machine which is connected to a network or to the internet. Adequate procedures must be in place for the handling of the certificate - for example access should always be on a "four eyes" basis, physical access to the machine containing the private keys must be controled and there must be procedures in place. All of this is documented in the EU's security policy [TODO LINK].

The `DSC` is the certificate that will be actually issuing the DCC. This must be stored on a secure but network connected system. In the ideal case it is stored on a Hardware Security Module (HSM). When that is not the case, the system it is stored on must be hardened. It goes without saying that the system must not be directly connected to the internet. All of the requirements are again documented in the EU's security policy [TODO LINK].

## Approach

So, you are issuing DIVOC / SHC and would like to use the same hardware, organisation and key to issue DCC? Luckily there is a way to do it.

The basic approach is this: you need to turn your DIVOC/SHC keypair into a `DSC`. So that means that it must be turned into an x509 certificate issued by your `CSCA`, which has in turn been issued by a respectable Certificate Authority. Wow, that sounds complex! It is - and that is where this guide comes in to help :)

This guide will help you first generate and procure a new `CSCA` certificate, either from your national Certificate Authority or from a reputable commercial Certificate Authority. Then it will show you how to use your shiny new `CSCA` certificate to issue your `DSC` based on *the existing keypair used for SHC/DIVOC*. Once this is done you can simply use the `CSCA` and `DSC` to onboard to the EU.

## How-to Guide

This guide assumes that you have your private RSA or ECDSA key that is used to sign DIVOC saved as `dsc.private` and the public key used for checking as `dsc.public`.

### Creating a CSCA certificate

We'll use a file-based key for this guide - ideally you'll be using a HSM. If you are, then you'll need to refer to the HSM manual 

1. Generate an RSA private key:

```
openssl genrsa > csca.private
```

2. Save the public key:

```
openssl rsa -in csca.private -pubout > csca.public
```

3. Generate a Certificate Signing Request (see: [or use LetsEncrypt for testing this script](#letsencrypt-tls-certificate))

```
openssl req -new -sha256 -key csca.private -subj /CN=CSCA -set_serial 1 > csca.csr
```

4. Use that CSR to order a new signing certificate from your CA and save the result as:

```
csca.pem
```

5. Delete the signing request:

```
rm csca.csr
```

Now you have the following files:

```
csca.private
csca.public
csca.pem
```

#### LetsEncrypt TLS certificate

You can use LetsEncrypt to sign your CSCA certificate for the purpose of testing this script. It is not acceptable for the EU onboarding but is a low-cost way to run through this guide.

LetsEncrypt generates TLS certificates for a domain. To use these certs you'll need:

* Domain name under your control.
* Ability to server files from an arbitary path on that domain.
* OR the ability to control the DNS records (and time to wait for it to propigate)

Generate your CSR using this command, replacing `subdomain.mydomain.tld` with your domain name (replacing step 3):

```openssl req -new -sha256 -key csca.private -subj /CN=subdomain.mydomain.tld -set_serial 1 > csca.csr```

Then follow the guide on this website, using your `csca.csr` in step 2 to generate the certificate:

	https://gethttpsforfree.com/

When saving the results, save the FIRST certificate (`-----BEGIN CERTIFICATE-----`) data into the file `csca.pem`  from step (4). The other certificates are the intermediates in the chain.

### Create a DSC

Optional: create a DSC private key (handy when testing this script, in reality you already have this key - you're using it to sign your DIVOC or SHC)

	openssl genrsa > dsc.private

Now you can create a singing request for the DSC:

	openssl req -new -subj /CN=Worker -key dsc.private -set_serial 10001 -out dsc.csr

And you can sign the request with tyour CSCA

	openssl x509 -req -in dsc.csr -CAkey csca.private -CA csca.pem -CAcreateserial -out dsc.pem

Which can be verified with:

	openssl x509 -in dsc.pem -text -noout 

If everything has worked then you should see your issuer in the certificate data!

## Thanks

Thanks to Dirk-Willem van Gulik for his help invaluable help with validating this approach and with providing all of the openssl voodoo :-)


