# DCC Issuance Guide

The EU DCC encodes a certificate that's issued for:

* Vaccination - or more precisely: a status summarizing a combination of multiple vaccinations, and possibly a recovery
* Recovery - after positive test, and confirmation by a health authority
* Test result - after negative test at an officially-registered testing facility

## General structure

The DCC is encoded as JSON.
This includes a section linking the identity of the holder to the DCC, and a section which includes a medical statement made about the holder - for example their vaccination status.

An example of a DCC is:

	{
  		"ver": "1.3.0",
  		"nam": {
    		"fn": "Smith-Jones",
    		"fnt": "SMITH<JONES",
    		"gn": "Charles Edward",
    		"gnt": "CHARLES<EDWARD"
  		},
  		"dob": "1964-01-01",
  		"v": [
    		{
      		"tg": "840539006",
      		"vp": "1119349007",
      		"mp": "EU/1/20/1528",
      		"ma": "ORG-100031184",
      		"dn": 1,
      		"sd": 2,
      		"dt": "2021-06-11",
      		"co": "NL",
      		"is": "Ministry of Health Welfare and Sport",
      		"ci": "URN:UVCI:01:NL:DADFCC47C7334E45A906DB12FD859FB7#1"
    		}
  		]
	}

The version number is stored in `ver` and it represents the version of the schema encoding used for the DCC.

	"ver": "1.3.0"

The holder's names are stored in the element `nam`: `fn` contains the family name and `gn` contains the given name of the holder encoded in any UTF-8 alphabet character.
The fields `fnt` and `gnt` contain the family name and given name respectively transliterated according to the [ICAO 9303 MRTD transliteration rules](https://www.icao.int/publications/Documents/9303_p3_cons_en.pdf).

	"nam": {
		"fn": "Smith-Jones",
		"fnt": "SMITH<JONES",
		"gn": "Charles Edward",
		"gnt": "CHARLES<EDWARD"
	}

The holder's date of birth is included in the `dob` element.
It is date-formatted according to ISO-8601 UTC and may contain either: `YYYY`, `YYYY-MM` or `YYYY-MM-DD`.
This date must match the date the holder's identity document.

	"dob": "1964-01-01"

In case the identity document contains `YYYY-XX-XX`, `YYYY-00-00` or other such partial dates, then only the known portion of the date should be encoded.

The medical data is stored in an array containing a single element.
The name of the array differs depending on the type of medical data.
For vaccinations, the array is `v`, for test it is `t`, and for recovery it is `r`.

There are a number elements that are shared by all of the medical data types:

| Field | Name                    | Description                                                |
| ----- | ----------------------- | ---------------------------------------------------------- |
| `tg`  | Targeted agent/disease  | Always `840539006`, SNOMED CT (GPS) code for COVID-19      |
| `co`  | Country of event        | ISO-3166-2 code of country where event happened            |
| `is`  | Issuer                  | Name of the institution under whose name the DCC is issued |
| `ci`  | Certificate id          | Unique ID, `UCVI:{version}:{country}:{random}:{checksum}`  |

The full certificate identifier specification can be found [here](https://ec.europa.eu/health/sites/default/files/ehealth/docs/vaccination-proof_interoperability-guidelines_en.pdf) and the official technical specifications for the EU-DCC schema is published [here](https://ec.europa.eu/health/sites/default/files/ehealth/docs/covid-certificate_json_specification_en.pdf). 


## Test (PCR)

The test data is stored under the `t` element.

	"t": [
		{
			"tg": "840539006",
			"tt": "LP6464-4",
			"nm": "ELITechGroup, SARS-CoV-2 ELITe MGB® Kit",
			"sc": "2021-06-11T17:30:00Z",
			"tr": "260415000",
			"tc": "Example Test Corp, Big City",
			"co": "NL",
			"is": "Ministry of Health Welfare and Sport",
			"ci": "URN:UVCI:01:NL:DADFCC47C7334E45A906DB12FD859FB7#1"
		}
	]

The fields which are unique to PCR tests are:

| Field | Name         | Description                                         |
| ----- | ------------ | --------------------------------------------------- |
| `tt`  | Test type    | Value from the PCR TT valueset found on the Gateway |
| `nm`  | Test name    | Optional, when included must not be empty or ``     |
| `sc`  | Sample date  | ISO-8601 date/time formats (see below)              |
| `tr`  | Test result  | Value from the PCR TT valueset found on the Gateway |
| `tc`  | Test center  | Actor who did the test, max 80 UTF-8 characters     |

The following are valid date/time formats:
- `YYYY-MM-DDThh:mm:ssZ`
- `YYYY-MM-DDThh:mm:ss[+-]hh`
- `YYYY-MM-DDThh:mm:ss[+-]hhmm`
- `YYYY-MM-DDThh:mm:ss[+-]hh:mm`
<!-- (This is a subset of all valid formats.) -->

## Test (Rapid Antigen/RAT)

The test data is stored as the first (and only) item under the `t` element.

	"t": [
		{
			"tg": "840539006",
			"tt": "LP217198-3",
			"ma": "532",
			"sc": "2021-06-11T17:30:00Z",
			"tr": "260415000",
			"tc": "Example Test Corp, Big City",
			"co": "NL",
			"is": "Ministry of Health Welfare and Sport",
			"ci": "URN:UVCI:01:NL:DADFCC47C7334E45A906DB12FD859FB7#1"
		}
	]

The fields which are unique to RAT tests are:

| Field | Name            | Description                                            |
| ----- | -------------- | ------------------------------------------------------- |
| `tt`  | Test type      | Value from the RAT TT valueset found on the Gateway     |
| `ma`  | Test device ID | RAT device id from the JRC DB, HSC common list valueset |
| `sc`  | Sample date    | ISO-8601 date/time formats (see below)                  |
| `tr`  | Test result    | Value from the PCR TT valueset found on the Gateway     |
| `tc`  | Test center    | Actor who did the test, max 80 UTF-8 characters         |

Extra information over the test device id:

In EU/EEA countries, issuers MUST only issue certificates for tests belonging to the currently valid value set.
The value set MUST be updated every 24 hours.

Values outside of the value set MAY be used in certificates issued by third countries, however the identifiers MUST still be from the JRC database.
The use of other identifiers such as those provided directly by test manufacturers is not permitted.


## Recovery

The recovery data is stored as the first (and only) item under the `r` element.

	"r": [
		{
			"tg": "840539006",
			"fr": "2021-06-01",
			"df": "2021-06-12",
			"du": "2021-11-28",
			"co": "NL",
			"is": "Ministry of Health Welfare and Sport",
			"ci": "URN:UVCI:01:NL:DADFCC47C7334E45A906DB12FD859FB7#1"
		}
	]

The fields which are unique to recovery are:

| Field | Name                           | Description                                   |
| ----- | ------------------------------ | --------------------------------------------- |
| `fr`  | Date of first positive result  | ISO-8601, `YYYY-MM-DD`                        |
| `df`  | Date Valid From                | ISO-8601, `YYYY-MM-DD`                        |
| `du`  | Date Valid To                  | ISO-8601, `YYYY-MM-DD`                        |

The recovery certificate can be issued for a person who has recovered from COVID-19, as proven by a positief NAAT/PCR test.
To quote the legislation:

"A certificate confirming that, following a positive result of a NAAT test carried out by health professionals or by skilled testing personnel the holder has recovered from a SARS-CoV-2 infection (certificate of recovery)."

## Vaccination

The vaccination data is stored as the first (and only) item under the `v` element.
	
	"v": [
		{
			"tg": "840539006",
			"vp": "1119349007",
			"mp": "EU/1/20/1528",
			"ma": "ORG-100031184",
			"dn": 1,
			"sd": 2,
			"dt": "2021-06-11",
			"co": "NL",
			"is": "Ministry of Health Welfare and Sport",
			"ci": "URN:UVCI:01:NL:DADFCC47C7334E45A906DB12FD859FB7#1"
		}
	]

The fields which are unique to a vaccination are:

| Field | Name                    | Description                                         |
| ----- | ----------------------- | --------------------------------------------------- |
| `vp`  | Vaccination/prophylaxis | Value from the v/p valueset found on the Gateway    |
| `mp`  | Medical product         | Value from the v/p valueset found on the Gateway    |
| `ma`  | Manufactorer/MAH        | Value from the v/p valueset found on the Gateway    |
| `dn`  | Dose number             | Number of doses holder has received                 |
| `sd`  | Total series            | Number of doses in the full series, always >= `dn`  |
| `dt`  | Date of event           | ISO-8601 UTC, `YYYY-MM-DD`                          |

For vaccinations the record contains the details of the last vaccination event along with a sequence counter.
The sequence counter identifies the vaccination count over the number of doses required for the vaccination.

For example, the sequence of Pfizer 1/2 will be encoded like this:

	{
  		"tg": "840539006",
  		"vp": "1119349007",
  		"mp": "EU/1/20/1528",
  		"ma": "ORG-100031184",
  		"dn": 1,
  		"sd": 2,
  		"dt": "2021-06-11",
  		"co": "NL",
  		"is": "Ministry of Health Welfare and Sport",
  		"ci": "URN:UVCI:01:NL:DADFCC47C7334E45A906DB12FD859FB7#1"
	}

And the sequence Pfizer 2/2 is encoded like this:

	{
  		"tg": "840539006",
  		"vp": "1119349007",
  		"mp": "EU/1/20/1528",
  		"ma": "ORG-100031184",
  		"dn": 2,
  		"sd": 2,
  		"dt": "2021-08-11",
  		"co": "NL",
  		"is": "Ministry of Health Welfare and Sport",
  		"ci": "URN:UVCI:01:NL:DADFCC47C7334E45A906DB12FD859FB7#1"
	}

In the cases that a person has received more than the standard sequence of doses, then the actual number of doses given will be provided, and `dn` will be equal to `sd`.

### Vaccine Boosters

Booster vaccinations MUST be encoded as an additional dose in the sequence. For Pfizer this results in a sequence of 3/3, which is encoded like this:

	{
  		"tg": "840539006",
  		"vp": "1119349007",
  		"mp": "EU/1/20/1528",
  		"ma": "ORG-100031184",
  		"dn": 2,
  		"sd": 2,
  		"dt": "2021-11-11",
  		"co": "NL",
  		"is": "Ministry of Health Welfare and Sport",
  		"ci": "URN:UVCI:01:NL:DADFCC47C7334E45A906DB12FD859FB7#1"
	}

A mini-app to see how the values of the `dn` and `sd` fields in the vaccination-part of a DCC should be computed according to recent EU Regulations:

https://dcc-encoding.vercel.app/

### Wait times

There is an issue regards wait time after last dose.
Many countries consider a person "fully vaccinated" only 14 days after their last vaccination, regardless of whether that vaccination was part of their primary vaccination course.
For people who have received a booster dose, this will mean that there might be a wait time before their booster DCC is accepted.

There are several ways to handle this situation:

* Issue multiple DCC, then the original complete vaccination can be used during the wait time.
* Educate citizens so that they know about the wait time.
* Get in contact with the business rules developers of the (intended) destination to check whether the wait time after a booster dose is intentional, and matching their official entry regulations.

### Encoding of Bivalent Vaccines.

In late 2022 a number of bivalent vaccination subtypes have come on to the market. A number of these vaccines have received market approval for use within the EU. The approval is an extention of the existing market approval as opposed to a completely new approval.

Base on the feedback received from the Health Security Committee the European Commission has decided that the bivalent vaccinations will be encoded within the DCC using the existing encoding of their supertype.

That means, for example, that if a person receives, as his or her second booster following a two-dose primary series, a dose of Comirnaty bivalent Original/Omicron BA.4/5, he or she should receive an EU Digital COVID Certificate stating that he or she has received a dose of Comirnaty using the existing code (EU/1/20/1528) and indicating “4/4” as the number of doses received.

Here is a full list of the (at time of writing) approved bivalent vaccinations and their encoding:

Vaccination name 							| Encoding
--------------------------------------------|-------------
Comirnaty bivalent Original/Omicron BA.1    | EU/1/20/1528
Comirnaty bivalent Original/Omicron BA.4/5  | EU/1/20/1528
Spikevax bivalent Original/Omicron BA.1     | EU/1/20/1507

(NOTE: if you are deploying a new bivalent vaccination not currently included on this list please feel free to create an issue in github and/or contact the maintainers of this repository and we will clarify the encoding).

In collaboration with the Health Security Committee, we will continue monitoring scientific evidence related to the use of these vaccines.

## Further reading

The official technical specification for the EU-DCC schema is published [here](https://ec.europa.eu/health/sites/default/files/ehealth/docs/covid-certificate_json_specification_en.pdf). That document is leading: the single point of truth. If you find any inconsistencies between that document and any other documentation in this repository, then please create an issue in the repository. Then it will be fixed.

