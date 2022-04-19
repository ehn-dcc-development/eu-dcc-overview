# eHealth Network: Digital Covid Certificates

Welcome to the official home of the EU DCC (Digital Covid Certificate) on Github. Here you will find the specifications of the DCC system - HCERT, Schema, Value sets, and Business Rules.

In addition, we gratefully host example implementations of various components of the DCC. We aim to provide examples in a broad range of languages and platforms and welcome any contributions. Do you have something that you would like to contribute? Then read [contributing.md](contributing.md) and get started today.

**If you just want to see how the entire system in action on your own computer: check out (this repository)[https://github.com/ehn-dcc-development/ehn-sign-verify-python-trivial].**

## Organization

![Relation between ](img/eHN-organisation.svg)

eHN defines, specifies, manages and provides sample implementations of the HCERT (the specification) and DCC (the EU implementation).
Member States (from the European Economic Area, which encompasses more countries than the EU Member States) implement the specifications.
TSi provide open source reference implementations of the DCC.
In addition, they operate the EU trust Gateway (DGCG) including onboarding services.

The sister organization [EU Digital Green Certificates](https://github.com/eu-digital-green-certificates) - managed by TSi - is the home of the official reference implementations of the DCC gateway, Holder and Verifier apps and associated tooling.

A number of Member States have open sourced their implementations, including their signing services, Holder apps and Verifier apps. You can find an overview of them here.

## Specifications

The DCC specifications have been split into four repositiros:

* [hcert-spec](https://github.com/ehn-dcc-development/hcert-spec) specifies the container and encoding formats used by the DCC.
* [ehn-dcc-valuesets](https://github.com/ehn-dcc-development/ehn-dcc-valuesets) provides the valueset datastructures and a snapshot of the valuesets (the current versions are published on the DCCG Gateway.
* [ehn-dcc-schema](https://github.com/ehn-dcc-development/ehn-dcc-schema) specifies the DCC schema (the specification of the data stored within the DCC).
* [dgc-business-rules](https://github.com/ehn-dcc-development/dgc-business-rules) specifies the Business Rules framework.

You can find these repositories pinned on our github [homepage](https://github.com/ehn-dcc-development).

We have a team of maintainers who monitor the above mentioned pinned repositories. If you have questions or suggestions please feel free to open an issue on the respective repository.

## Maintainers

The core repoisotires are maintained a small group of people attached to EU Member States or the European Commission. 

Repository          | Teams
------------------- | ------------
hcert-spec          | [hcert-spec-editors](https://github.com/orgs/ehn-dcc-development/teams/hcert-spec-editors)
ehn-dcc-valuesets   | [ehn-valueset-editors](https://github.com/orgs/ehn-dcc-development/teams/ehn-valueset-editors)
ehn-dcc-schema      | [ehn-schema-editors](https://github.com/orgs/ehn-dcc-development/teams/ehn-schema-editors)
dgc-business-rules  | [ehn-business-rules-editors](https://github.com/orgs/ehn-dcc-development/teams/ehn-business-rules-editors)
overview            | [overview-editors](https://github.com/orgs/ehn-dcc-development/teams/overview-editors)

The remaining repositories have been donated by parties and are maintained by them. Feel free to raise issues on them, if you feel that they need the attention of the core team (for example you have found a bug) then you may also raise an issue on this repository (the overview).

## Documentation, guides, FAQs and How-Tos

There is a large amount of documentation and knowledge of the DCC but it can be hard to find. We maintain an overview of important documentation here to help with this.

If you have questions or find something hard to understand please feel make an issue on this repository and we'll see if we can improve the documentation to help.

Document | Description
-------- | ----------------------
[EC eHealth and COVID-19](https://ec.europa.eu/health/ehealth-digital-health-and-care/ehealth-and-covid-19_en) | Homepage of EU-DCC at the EC, here you'll find all of the officially published technical documentation as well as the various forms and procedures required for onboaring of non-EU countries (referred to as "Third Countries" in EC parlance)
[Implementing decisions](https://ec.europa.eu/info/publications/commission-implementing-decisions-eu-equivalence-covid-19-certificates-issued-non-eu-countries_en) | Implementing decisions between the EU and Third Countries are published here.
[Key documents](https://ec.europa.eu/info/publications/key-documents-related-digital-covid-19-certificate_en) | Key legal documents, ammendments etc

In the [FAQ](/faq) folder you can find answers to a number of common questions as well as a selection of detailed guides and "how tos" written and maintained by the core team.

### FAQs

Link        | Description
---------   | ------------------
[Convert public/private keypair to x509 chain](faq/convert-public-private-keypair-to-x509-certificate-chain.md) | Technical guide to converting a public/private keypair into a full x509 certificate chain. Useful if you have existing infrastructure which you wish to issue DCCs from your existing DIVOC or SHC system.
[Create a valid DCC payload](faq/create-a-valid-dcc-payload.md) | Guide to issuing a valid DCC, this covers a lot of edge cases such as how to correctly encode boosters and vaccination DCC based on combinations of recovery/vaccination.

## Overview of Implementations

In this repository we host a number of implementations of the DCC and forks of a several implementations which are used in production DCC deployments by various Member States.

If you're getting started or just want to under how the DCC fits together then check out the fully worked example of the full stack [here](https://github.com/ehn-dcc-development/ehn-sign-verify-python-trivial). It's written in simple Python and is easy to understand even for those with little Python experience.

Here's an overview of most of the repositories under the organisation. Please refer to the repositories themselves for information on licenses, contribution policies etc.

Repository                                                                          | Description
---------                                                                           | -----------------
[base45-ansi-c](https://github.com/ehn-dcc-development/base45-ansi-C)               | base45 encoder/decoder in C
[base45-cs](https://github.com/ehn-dcc-development/base45-cs)                       | base45 encoder/decoder in C#
[base45-java](https://github.com/ehn-dcc-development/base45-java)                   | base45 encoder/decoder in Java
[base45-js](https://github.com/ehn-dcc-development/base45-js)                       | base45 encoder/decoder in Javascript
[base45-php](https://github.com/ehn-dcc-development/base45-php)                     | base45 encoder/decoder in PHP
[base45-swift](https://github.com/ehn-dcc-development/base45-swift)                 | base45 encoder/decoder in Swift
[DccCachingService](https://github.com/ehn-dcc-development/DccCachingService)       | Caches trustlist, business rules, valuesets on the device built in Swift (iOS)
[DGCValidator](https://github.com/ehn-dcc-development/DGCValidator)                 | Cross-platform verifier app build in Xamarin
[hcert-app-swift](https://github.com/ehn-dcc-development/hcert-app-swift)           | HCERT validation app in Swift (iOS)
[hcert-app-kotlin](https://github.com/ehn-dcc-development/hcert-app-kotlin)         | HCERT validation app in Kotlin (Android)
[hcert-dotnet](https://github.com/ehn-dcc-development/hcert-dotnet)                 | HCERT validation and creation in C# (Xamarin/server)
[hcert-java](https://github.com/ehn-dcc-development/hcert-java)                     | HCERT validation and creation in Java (Android/server)
[hcert-kotlin](https://github.com/ehn-dcc-development/hcert-kotlin)                 | HCERT validation and creation in Kotlin (Android)
[hcert-service-kotlin](https://github.com/ehn-dcc-development/hcert-service-kotlin) | HCERT creation service (server) , deployed [here](https://dgc.a-sit.at/ehn/)
[icao-ml-Tools](https://github.com/ehn-dcc-development/icao-ml-tools) | Scripts to download, parse and cross-reference public ICAO master lists
[python-hcert](https://github.com/ehn-dcc-development/python-hcert)                 | HCERT validation and creation in Python (server)
[ValidationCore](https://github.com/ehn-dcc-development/ValidationCore)             | Validation libraries for both iOS and Android
[x509-resign](https://github.com/ehn-dcc-development/x509-resign)                   | Tool to resign x509 cert
[ehn-dcc-vsu](https://github.com/ehn-dcc-development/ehn-dcc-vsu)                   | Prototype client-server to demonstrate how clients can update to the latest Digital COVID Certificate (DCC) value sets
[ehn-ecdsa-verify-mbed](https://github.com/ehn-dcc-development/ehn-ecdsa-verify-mbed) | Simple MBED-TLS code for verifying an ECDSA signature as used in DCCs

## Useful third-party libraries

### CBOR

[CBOR Object Signing and Encryption (COSE)](https://tools.ietf.org/id/draft-ietf-cose-rfc8152bis-struct-00.html) is used data format for the DCC payload and signature. 

Various implementors make use of the Javascript implementation [erdtman/cose-js](https://github.com/erdtman/cose-js) (forked [here](https://github.com/ehn-dcc-development/cose-js)) and the Java implementation [cose-wg/COSE-JAVA](https://github.com/cose-wg/COSE-JAVA) (forked [here](https://github.com/ehn-dcc-development/COSE-JAVA))

# TODO

Rename https://github.com/ehn-dcc-development/hcert-spec > ehn-dcc-hcert-spec

Rename https://github.com/ehn-dcc-development/ehn-sign-verify-python-trivial to something clearer

Rename https://github.com/ehn-dcc-development/hcert-app-kotlin > verifier-app-kotlin

Rename https://github.com/ehn-dcc-development/dgc-business-rules > dcc-business-rules

* Add Trustlist overview (document linking to trustlists from various states who publish)
