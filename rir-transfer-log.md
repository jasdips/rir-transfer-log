%%%
Title = "Regional Internet Registry (RIR) Transfer Log"
abbrev = "RIR Transfer Log"
ipr= "trust200902"

[seriesInfo]
name = "Internet-Draft"
value = "rir-transfer-log-01"
stream = "IETF"
status = "standard"
date = 2026-05-20T00:00:00Z

[[author]]
organization="Number Resource Organization"
[author.address]
email = "secretariat@nro.net"

%%%

.# Abstract

This document specifies backwards-compatible version 4.1 of the Transfer Log JSON document that each Regional Internet
Registry (RIR) produces to publish completed and in-progress intra-RIR and inter-RIR transfers of IP addresses and
Autonomous System Numbers (ASNs) for a given period. It utilizes JSON Schema to formally define the format.

{mainmatter}

# Requirements Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",
"NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14
[@!RFC2119] [@!RFC8174] when, and only when, they appear in all capitals, as shown here.

Indentation and whitespace in examples are provided only to illustrate element relationships, and are not a required
feature of this specification.

"..." in examples is used as shorthand for elements defined outside of this document.

# Introduction

This document specifies backwards-compatible version 4.1 of the Transfer Log JSON document that each Regional Internet
Registry (RIR) produces to publish completed and in-progress intra-RIR and inter-RIR transfers of IP addresses and
Autonomous System Numbers (ASNs) for a given period. It utilizes JSON Schema [@!I-D.ietf-jsonschema-json-schema] to
formally define the format.

Salient changes from version 4.0 of Transfer Log [@!TRANSFER-LOG-4] are:

* Added an optional "transfers_in_progress" member to track in-progress intra-RIR and inter-RIR transfers (see
  (#in_progress_transfers)).
* Added an optional "UTC_offset_minutes" member to the "version" metadata object to support minute-level granularity,
  in addition to the existing "UTC_offset" member representing an offset in whole hours (see (#metadata)).
* The format is now defined using JSON Schema instead of JSON Content Rules (JCR) (see (#json_schema)).

# Common Data Members {#common_data_members}

The JSON defined in (#format) can contain the following common members:

* "asns" -- An object representing ASNs, with the following members:
    * "original_set" -- (OPTIONAL) An array of objects, each representing a contiguous block of ASNs.
    * "transfer_set" -- (REQUIRED) An array of objects, each representing a contiguous block of ASNs.

        Both "original_set" and "transfer_set" arrays contain objects with the following members:
        * "start" -- (REQUIRED) An unsigned 32-bit integer representing the start ASN.
        * "end" -- (REQUIRED) An unsigned 32-bit integer representing the end ASN.
* "ip4nets" -- An object representing IPv4 addresses, with the following members:
    * "original_set" -- (OPTIONAL) An array of objects, each representing a contiguous block of IPv4 addresses.
    * "transfer_set" -- (REQUIRED) An array of objects, each representing a contiguous block of IPv4 addresses.

        Both "original_set" and "transfer_set" objects contain objects with the following members:
        * "start_address" -- (REQUIRED) A string representing the start IPv4 address.
        * "end_address" -- (REQUIRED) A string representing the end IPv4 address.
        * "cidrs" -- (OPTIONAL) An array for IPv4 CIDR blocks which make up this IP block. Each IPv4 CIDR block object
          in this array has the following members:
            * "prefix" -- (REQUIRED) A string representing the IPv4 CIDR prefix.
            * "length" -- (REQUIRED) An integer representing the IPv4 CIDR length, with value from 0 to 32.
* "ip6nets" -- An object representing IPv6 addresses, with the following members:
    * "original_set" -- (OPTIONAL) An array of objects, each representing a contiguous block of IPv6 addresses.
    * "transfer_set" -- (REQUIRED) An array of objects, each representing a contiguous block of IPv6 addresses.

        Both "original_set" and "transfer_set" objects contain objects with the following members:
        * "start_address" -- (REQUIRED) A string representing the start IPv6 address.
        * "end_address" -- (REQUIRED) A string representing the end IPv6 address.
        * "cidrs" -- (OPTIONAL) An array for IPv6 CIDR blocks which make up this IP block. Each IPv6 CIDR block object
          in this array has the following members:
            * "prefix" -- (REQUIRED) A string representing the IPv6 CIDR prefix.
            * "length" -- (REQUIRED) An integer representing the IPv6 CIDR length, with value from 0 to 128.
* "source_organization" -- An object representing an organization that is the source of the transfer.
* "recipient_organization" -- An object representing an organization that is the recipient of the transfer.

    Both "source_organization" and "recipient_organization" objects have the following members:
    * "name" -- (OPTIONAL) A string representing the name of the organization.
    * "country_code" -- (OPTIONAL) A string representing the 2-letter country code of the organization.
* "source_registration_date" -- A string representing the date and time of the registration of source resources in the
  source RIR, per the date-time ABNF rule from [@!RFC3339], with a UTC offset of +00:00, denoted by a time-offset ABNF
  rule value of "Z".
* "source_rir" -- A string identifying the source RIR, with valid values of "AFRINIC", "APNIC", "ARIN", "LACNIC", or
  "RIPE NCC".
* "recipient_rir" -- A string identifying the recipient RIR, with valid values of "AFRINIC", "APNIC", "ARIN", "LACNIC",
  or "RIPE NCC".
* "type" -- A string representing the type of transfer, with valid values of "MERGER_ACQUISITION" or
  "RESOURCE_TRANSFER". "MERGER_ACQUISITION" indicates a transfer resulting from corporate restructuring, mergers,
  acquisitions, or legal identity changes, whereas "RESOURCE_TRANSFER" indicates a direct contractual transfer of
  specific number resources between independent entities.

An "original_set" object describes the set of resources in the registry from which the related "transfer_set" is taken.
While these sets are often equivalent, the "original_set" can be larger than the "transfer_set". There are transfers in
which the "original_set" is unknown: the recipient RIR may not have knowledge of the "original_set" in the registry of
the source RIR.

# Format {#format}

In a Transfer Log JSON document, the root object MUST contain the "version" and "transfers" members, and MAY contain
the "transfers_in_progress" member.

## Metadata {#metadata}

The "version" member is an object representing the metadata information for the produced document, and has the following
members:

* "stats_version" -- (REQUIRED) A string representing the version of the Transfer Log JSON document, with a value of
  "4.1" for this version.
* "producer" -- (REQUIRED) A string identifying the organization, either one of the RIRs or the NRO, that produced the
  Transfer Log JSON document, with valid values of "AFRINIC", "APNIC", "ARIN", "LACNIC", "RIPE NCC", or "NRO".
* "UTC_offset" -- (REQUIRED) An integer representing the UTC offset of the producer in whole hours, with value from -12
  to 12.
* "UTC_offset_minutes" -- (OPTIONAL) An integer representing the additional minutes component of the UTC offset, with a
  value from 0 to 59. If "UTC_offset" is negative, this minutes value MUST also be applied as a negative offset. For
  example, "UTC_offset" set to -5 and "UTC_offset_minutes" set to 30 represents a -05:30 offset.
* "production_date" -- (REQUIRED) A string containing the date and time at which the document was produced, per the
  date-time ABNF rule from [@!RFC3339], with a UTC offset of +00:00, denoted by a time-offset ABNF rule value of "Z".
* "records_interval" -- (REQUIRED) An object representing the period for which the records in the document are covered,
  with the following members:
    * "start_date" -- (REQUIRED) A string representing the start date and time for which the records in the document are
      covered, per the date-time ABNF rule from [@!RFC3339], with a UTC offset of +00:00, denoted by a time-offset ABNF
      rule value of "Z".
    * "end_date" -- (REQUIRED) A string representing the end date and time for which the records in the document are
      covered, per the date-time ABNF rule from [@!RFC3339], with a UTC offset of +00:00, denoted by a time-offset ABNF
      rule value of "Z".
* "remarks" -- (OPTIONAL) An array of strings, where each string contains a semantically distinct prose (in some
  contexts, these might be called paragraphs). This is where the producer would put copyright notices, terms of service
  for the data, and production notes.

## Completed Transfers

The "transfers" member is an array of objects for completed intra-RIR and inter-RIR transfers. Each object within this
array has the following members:

* "asns" -- (OPTIONAL) See (#common_data_members).
* "ip4nets" -- (OPTIONAL) See (#common_data_members).
* "ip6nets" -- (OPTIONAL) See (#common_data_members).
* "source_organization" -- (OPTIONAL) See (#common_data_members).
* "recipient_organization" -- (OPTIONAL) See (#common_data_members).
* "transfer_date" -- (REQUIRED) A string representing the date and time of the completed transfer, per the date-time
  ABNF rule from [@!RFC3339], with a UTC offset of +00:00, denoted by a time-offset ABNF rule value of "Z".
* "source_registration_date" -- (OPTIONAL) See (#common_data_members).
* "source_rir" -- (REQUIRED) See (#common_data_members).
* "recipient_rir" -- (REQUIRED) See (#common_data_members).
* "type" -- (REQUIRED) See (#common_data_members).

Each completed transfer object MUST contain at least one of the "asns", "ip4nets", or "ip6nets" objects.

The "source_rir" and "recipient_rir" values MUST be identical for an intra-RIR transfer and distinct for an inter-RIR
transfer.

## In-Progress Transfers {#in_progress_transfers}

The "transfers_in_progress" member is an array of objects for in-progress intra-RIR and inter-RIR transfers. Each object
within this array has the following members:

* "asns" -- (OPTIONAL) See (#common_data_members).
* "ip4nets" -- (OPTIONAL) See (#common_data_members).
* "ip6nets" -- (OPTIONAL) See (#common_data_members).
* "source_organization" -- (OPTIONAL) See (#common_data_members).
* "recipient_organization" -- (OPTIONAL) See (#common_data_members).
* "source_registration_date" -- (OPTIONAL) See (#common_data_members).
* "source_rir" -- (REQUIRED) See (#common_data_members).
* "recipient_rir" -- (REQUIRED) See (#common_data_members).
* "type" -- (REQUIRED) See (#common_data_members).
* "status" -- (REQUIRED) A string representing the status of a transfer, with valid values of "SOURCE_INITIALIZED"
  for the source side or "RECIPIENT_ACCEPTED" for the recipient side.

Each in-progress transfer object MUST contain at least one of the "asns", "ip4nets", or "ip6nets" objects.

The "source_rir" and "recipient_rir" values MUST be identical for an intra-RIR transfer and distinct for an inter-RIR
transfer.

When a source organization initializes a transfer, the "status" value for the corresponding in-progress transfer object
MUST be set to "SOURCE_INITIALIZED". At this point, only the source organization has a claim over the resources being
transferred.

When a recipient organization accepts that transfer, the "status" value for the corresponding in-progress transfer
object MUST be set to "RECIPIENT_ACCEPTED". At this point, both the source and recipient organizations have a claim over
the resources being transferred.

When the source organization finalizes that transfer, it relinquishes its claim over the transferred resources, leaving
the recipient organization with sole claim over those resources. Furthermore, the corresponding in-progress transfer
object MUST be removed from the "transfers_in_progress" array. A corresponding completed transfer object MUST then be
created in the "transfers" array of the involved RIRs' transfer logs: the single RIR's transfer log for an intra-RIR
transfer, or both the source and recipient RIRs' transfer logs for an inter-RIR transfer.

If that transfer is canceled at any point prior to finalization by the source, the corresponding in-progress transfer
object MUST be removed from the "transfers_in_progress" array, leaving the source organization with sole claim over
those resources.

# JSON Schema {#json_schema}

The following JSON Schema formally defines the format of a Transfer Log JSON document:

```
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://nro.net/rir_transfer_log.schema.json",
  "title": "RIR Transfer Log",
  "description": "Schema to describe intra-RIR and inter-RIR transfers of IP addresses and ASNs",
  "type": "object",
  "required": [
    "version",
    "transfers"
  ],
  "properties": {
    "version": {
      "type": "object",
      "required": [
        "stats_version",
        "producer",
        "UTC_offset",
        "production_date",
        "records_interval"
      ],
      "properties": {
        "stats_version": {
          "type": "string",
          "const": "4.1"
        },
        "producer": {
          "type": "string",
          "enum": ["AFRINIC", "APNIC", "ARIN", "LACNIC", "RIPE NCC", "NRO"]
        },
        "UTC_offset": {
          "type": "integer",
          "minimum": -12,
          "maximum": 12
        },
        "UTC_offset_minutes": {
          "type": "integer",
          "minimum": 0,
          "maximum": 59
        },
        "production_date": {
          "type": "string",
          "format": "date-time",
          "pattern": "Z$"
        },
        "records_interval": {
          "type": "object",
          "required": ["start_date", "end_date"],
          "properties": {
            "start_date": {
              "type": "string",
              "format": "date-time",
              "pattern": "Z$"
            },
            "end_date": {
              "type": "string",
              "format": "date-time",
              "pattern": "Z$"
            }
          }
        },
        "remarks": {
          "type": "array",
          "items": {
            "type": "string"
          }
        }
      }
    },
    "transfers": {
      "type": "array",
      "items": {
        "type": "object",
        "required": [
          "transfer_date",
          "source_rir",
          "recipient_rir",
          "type"
        ],
        "anyOf": [
          { "required": ["asns"] },
          { "required": ["ip4nets"] },
          { "required": ["ip6nets"] }
        ],
        "properties": {
          "asns": { "$ref": "#/$defs/asns" },
          "ip4nets": { "$ref": "#/$defs/ip4nets" },
          "ip6nets": { "$ref": "#/$defs/ip6nets" },
          "source_organization": { "$ref": "#/$defs/organization" },
          "recipient_organization": { "$ref": "#/$defs/organization" },
          "transfer_date": {
            "type": "string",
            "format": "date-time",
            "pattern": "Z$"
          },
          "source_registration_date": {
            "type": "string",
            "format": "date-time",
            "pattern": "Z$"
          },
          "source_rir": { "$ref": "#/$defs/rir_names" },
          "recipient_rir": { "$ref": "#/$defs/rir_names" },
          "type": { "$ref": "#/$defs/transfer_types" }
        }
      }
    },
    "transfers_in_progress": {
      "type": "array",
      "items": {
        "type": "object",
        "required": [
          "source_rir",
          "recipient_rir",
          "type",
          "status"
        ],
        "anyOf": [
          { "required": ["asns"] },
          { "required": ["ip4nets"] },
          { "required": ["ip6nets"] }
        ],
        "properties": {
          "asns": { "$ref": "#/$defs/asns" },
          "ip4nets": { "$ref": "#/$defs/ip4nets" },
          "ip6nets": { "$ref": "#/$defs/ip6nets" },
          "source_organization": { "$ref": "#/$defs/organization" },
          "recipient_organization": { "$ref": "#/$defs/organization" },
          "source_registration_date": {
            "type": "string",
            "format": "date-time",
            "pattern": "Z$"
          },
          "source_rir": { "$ref": "#/$defs/rir_names" },
          "recipient_rir": { "$ref": "#/$defs/rir_names" },
          "type": { "$ref": "#/$defs/transfer_types" },
          "status": {
            "type": "string",
            "enum": ["SOURCE_INITIALIZED", "RECIPIENT_ACCEPTED"]
          }
        }
      }
    }
  },

  "$defs": {
    "rir_names": {
      "type": "string",
      "enum": ["AFRINIC", "APNIC", "ARIN", "LACNIC", "RIPE NCC"]
    },
    "transfer_types": {
      "type": "string",
      "enum": ["MERGER_ACQUISITION", "RESOURCE_TRANSFER"]
    },
    "organization": {
      "type": "object",
      "properties": {
        "name": { "type": "string" },
        "country_code": {
          "type": "string",
          "pattern": "^[A-Z]{2}$"
        }
      }
    },
    "asn_range": {
      "type": "object",
      "required": ["start", "end"],
      "properties": {
        "start": {
          "type": "integer",
          "minimum": 0,
          "maximum": 4294967295
        },
        "end": {
          "type": "integer",
          "minimum": 0,
          "maximum": 4294967295
        }
      }
    },
    "asns": {
      "type": "object",
      "required": ["transfer_set"],
      "properties": {
        "original_set": {
          "type": "array",
          "items": { "$ref": "#/$defs/asn_range" }
        },
        "transfer_set": {
          "type": "array",
          "items": { "$ref": "#/$defs/asn_range" }
        }
      }
    },
    "ip4_range": {
      "type": "object",
      "required": ["start_address", "end_address"],
      "properties": {
        "start_address": {
          "type": "string",
          "format": "ipv4"
        },
        "end_address": {
          "type": "string",
          "format": "ipv4"
        },
        "cidrs": {
          "type": "array",
          "items": {
            "type": "object",
            "required": ["prefix", "length"],
            "properties": {
              "prefix": {
                "type": "string",
                "format": "ipv4"
              },
              "length": {
                "type": "integer",
                "minimum": 0,
                "maximum": 32
              }
            }
          }
        }
      }
    },
    "ip4nets": {
      "type": "object",
      "required": ["transfer_set"],
      "properties": {
        "original_set": {
          "type": "array",
          "items": { "$ref": "#/$defs/ip4_range" }
        },
        "transfer_set": {
          "type": "array",
          "items": { "$ref": "#/$defs/ip4_range" }
        }
      }
    },
    "ip6_range": {
      "type": "object",
      "required": ["start_address", "end_address"],
      "properties": {
        "start_address": {
          "type": "string",
          "format": "ipv6"
        },
        "end_address": {
          "type": "string",
          "format": "ipv6"
        },
        "cidrs": {
          "type": "array",
          "items": {
            "type": "object",
            "required": ["prefix", "length"],
            "properties": {
              "prefix": {
                "type": "string",
                "format": "ipv6"
              },
              "length": {
                "type": "integer",
                "minimum": 0,
                "maximum": 128
              }
            }
          }
        }
      }
    },
    "ip6nets": {
      "type": "object",
      "required": ["transfer_set"],
      "properties": {
        "original_set": {
          "type": "array",
          "items": { "$ref": "#/$defs/ip6_range" }
        },
        "transfer_set": {
          "type": "array",
          "items": { "$ref": "#/$defs/ip6_range" }
        }
      }
    }
  }
}
```

# Example

```
{
  "version": {
    "stats_version": "4.1",
    "UTC_offset": -4,
    "UTC_offset_minutes": 0,
    "producer": "ARIN",
    "production_date": "2026-04-15T03:59:11Z",
    "records_interval": {
      "start_date": "2009-10-12T18:09:00Z",
      "end_date": "2026-04-14T18:59:21Z"
    },
    "remarks": [
      "Copyright (c) 2026 Example Registry",
      "Example Registry's Terms of Service for this Transfer Log JSON document can be found at https://registry.example/tos"
    ]
  },
  "transfers": [
    {
      "asns": {
        "original_set": [
          {
            "start": 65536,
            "end": 65539
          }
        ],
        "transfer_set": [
          {
            "start": 65536,
            "end": 65537
          }
        ]
      },
      "recipient_organization": {
        "name": "Example1 Inc.",
        "country_code": "US"
      },
      "recipient_rir": "ARIN",
      "source_organization": {
        "name": "Example2 Inc.",
        "country_code": "AU"
      },
      "source_registration_date": "2001-05-18T04:00:00Z",
      "source_rir": "APNIC",
      "transfer_date": "2012-08-06T17:46:00Z",
      "type": "MERGER_ACQUISITION"
    },
    {
      "ip6nets": {
        "original_set": [
          {
            "start_address": "2001:db8::",
            "end_address": "2001:db8:0:ffff:ffff:ffff:ffff:ffff"
          }
        ],
        "transfer_set": [
          {
            "start_address": "2001:db8::",
            "end_address": "2001:db8:0:ffff:ffff:ffff:ffff:ffff"
          }
        ]
      },
      "recipient_organization": {
        "name": "Example3 Inc.",
        "country_code": "US"
      },
      "recipient_rir": "ARIN",
      "source_organization": {
        "name": "Example4 Inc.",
        "country_code": "US"
      },
      "source_registration_date": "2011-04-19T20:59:54Z",
      "source_rir": "ARIN",
      "transfer_date": "2015-06-22T18:23:56Z",
      "type": "MERGER_ACQUISITION"
    }
  ],
  "transfers_in_progress": [
    {
      "asns": {
        "original_set": [
          {
            "start": 65550,
            "end": 65550
          }
        ],
        "transfer_set": [
          {
            "start": 65550,
            "end": 65550
          }
        ]
      },
      "recipient_organization": {
        "name": "Example5 Inc.",
        "country_code": "US"
      },
      "recipient_rir": "ARIN",
      "source_organization": {
        "name": "Example6 Inc.",
        "country_code": "AU"
      },
      "source_registration_date": "2002-05-18T04:00:00Z",
      "source_rir": "APNIC",
      "transfer_date": "2013-08-06T17:46:00Z",
      "type": "MERGER_ACQUISITION",
      "status": "RECIPIENT_ACCEPTED"
    },
    {
      "ip4nets": {
        "original_set": [
          {
            "start_address": "192.0.2.0",
            "end_address": "192.0.2.255"
          }
        ],
        "transfer_set": [
          {
            "start_address": "192.0.2.0",
            "end_address": "192.0.2.127"
          }
        ]
      },
      "recipient_organization": {
        "name": "Example7 Inc.",
        "country_code": "NL"
      },
      "recipient_rir": "RIPE NCC",
      "source_organization": {
        "name": "Example8 Inc.",
        "country_code": "CA"
      },
      "source_registration_date": "2013-04-01T21:13:31Z",
      "source_rir": "ARIN",
      "type": "RESOURCE_TRANSFER",
      "status": "SOURCE_INITIALIZED"
    }
  ]
}
```

In this example:

* The object with the "asns" member in the "transfers" array illustrates a completed inter-RIR transfer for the
  recipient side.
* The object with the "ip6nets" member in the "transfers" array illustrates a completed intra-RIR transfer.
* The object with the "asns" member in the "transfers_in_progress" array illustrates an in-progress inter-RIR transfer
  with the "RECIPIENT_ACCEPTED" status for the recipient side.
* The object with the "ip4nets" member in the "transfers_in_progress" array illustrates an in-progress inter-RIR
  transfer with the "SOURCE_INITIALIZED" status for the source side.

This example validates against the JSON Schema in (#json_schema), per [@JSON-SCHEMA-VALIDATOR].

{backmatter}

<reference anchor='TRANSFER-LOG-4' target='https://gitlab.nro.net/ecg/transfer_log/-/blob/master/transfer_log.jcr'>
    <front>
        <title>Transfer Log 4.0</title>
        <author>
            <organization>NRO</organization>
        </author>
    </front>
</reference>

<reference anchor='JSON-SCHEMA-VALIDATOR' target='https://www.jsonschemavalidator.net/'>
    <front>
        <title>JSON Schema Validator</title>
        <author>
            <organization>Newton-King, J.</organization>
        </author>
    </front>
</reference>
