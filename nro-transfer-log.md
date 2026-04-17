%%%
Title = "Regional Internet Registry (RIR) Transfer Log"
abbrev = "RIR Transfer Log"
ipr= "trust200902"

[seriesInfo]
name = "Internet-Draft"
value = "rir-transfer-log-00"
stream = "IETF"
status = "standard"
date = 2026-04-16T00:00:00Z

[[author]]
organization="Number Resource Organization"
[author.address]
email = "secretariat@nro.net"

%%%

.# Abstract

This document specifies the format of the JSON document that each Regional Internet Registry (RIR) produces to publish
intra-RIR and inter-RIR transfers of IP addresses and Autonomous System Numbers (ASNs) for a given period. It utilizes
JSON Schema to formally define this format.

{mainmatter}

# Requirements Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",
"NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14
[@!RFC2119] [@!RFC8174] when, and only when, they appear in all capitals, as shown here.

Indentation and whitespace in examples are provided only to illustrate element relationships, and are not a required
feature of this specification.

"..." in examples is used as shorthand for elements defined outside of this document.

# Introduction

This document specifies the format of the JSON document that each RIR produces to publish intra-RIR and inter-RIR
transfers of IP addresses and ASNs for a given period. It utilizes JSON Schema [@!I-D.dusseault-json-schema] to formally
define this format.

# Format

In a transfer log JSON document, the root object MUST contain two objects: a "version" object and a "transfers" object.

The "version" object is the metadata information for the produced document, and has the following members:

* "stats_version" -- (REQUIRED) A string representing the version of the specification, with a value of "5.0" for this
  version.
* "producer" -- (REQUIRED) A string identifying the organization, either one of the RIRs or the NRO, that produced the
  transfer log document, with possible values of "AFRINIC", "APNIC", "ARIN", "LACNIC", "RIPE NCC", or "NRO".
* "UTC_offset" -- (REQUIRED) An integer representing the UTC offset of the producer, with value from the -12 to 12
  range.
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
  contexts, these might be called paragraphs). This is where a producer would put copyright notices, terms of service
  for the data, and production notes.

The "transfers" object is an array of transfer objects. Each transfer object has the following members:

* "asns" -- (OPTIONAL) Such an object has the following members:
    * "original_set" -- (OPTIONAL) An array of objects, each representing a contiguous block of ASNs.
    * "transfer_set" -- (REQUIRED) An array of objects, each representing a contiguous block of ASNs.
      Both "original_set" and "transfer_set" arrays contain objects with the following members:
        * "start" -- (REQUIRED) An unsigned 32-bit integer representing the start ASN.
        * "end" -- (REQUIRED) An unsigned 32-bit integer representing the end ASN.
* "ip4nets" -- (OPTIONAL) Such an object has the following members:
    * "original_set" -- (OPTIONAL) An array of objects, each representing a contiguous block of IPv4 addresses.
    * "transfer_set" -- (REQUIRED) An array of objects, each representing a contiguous block of IPv4 addresses.
      Both "original_set" and "transfer_set" objects contain objects with the following members:
        * "start_address" -- (REQUIRED) A string representing the start IPv4 address.
        * "end_address" -- (REQUIRED) A string representing the end IPv4 address.
        * "cidrs" -- (OPTIONAL) An array for IPv4 CIDR blocks which make up this IP block. Each IPv4 CIDR block object
          in this array has the following members:
            * "prefix" -- (REQUIRED) A string representing the IPv4 CIDR prefix.
            * "length" -- (REQUIRED) An integer representing the IPv4 CIDR length, with value from the 0 to 32 range.
* "ip6nets" -- (OPTIONAL) Such an object has the following members:
    * "original_set" -- (OPTIONAL) An array of objects, each representing a contiguous block of IPv6 addresses.
    * "transfer_set" -- (REQUIRED) An array of objects, each representing a contiguous block of IPv6 addresses.
      Both "original_set" and "transfer_set" objects contain objects with the following members:
        * "start_address" -- (REQUIRED) A string representing the start IPv6 address.
        * "end_address" -- (REQUIRED) A string representing the end IPv6 address.
        * "cidrs" -- (OPTIONAL) An array for IPv6 CIDR blocks which make up this IP block. Each IPv6 CIDR block object
          in this array has the following members:
            * "prefix" -- (REQUIRED) A string representing the IPv6 CIDR prefix.
            * "length" -- (REQUIRED) An integer representing the IPv6 CIDR length, with value from the 0 to 128 range.
* "source_organization" -- (REQUIRED) An object representing an organization that is the source of the transfer.
* "recipient_organization" -- (REQUIRED) An object representing an organization that is the recipient of the transfer.
  Both "source_organization" and "recipient_organization" objects have the following members:
    * "name" -- (OPTIONAL) A string representing the name of the organization.
    * "country_code" -- (OPTIONAL) A string representing the 2-letter country code of the organization.
* "transfer_date" -- (OPTIONAL) A string representing the date and time of the transfer, per the date-time ABNF rule
  from [@!RFC3339], with a UTC offset of +00:00, denoted by a time-offset ABNF rule value of "Z".
* "source_registration_date" -- (OPTIONAL) A string representing the date and time of the registration of source
  resources in the source RIR, per the date-time ABNF rule from [@!RFC3339], with a UTC offset of +00:00, denoted by a
  time-offset ABNF rule value of "Z".
* "source_rir" -- (REQUIRED) A string identifying the source RIR, with possible values of "AFRINIC", "APNIC", "ARIN",
  "LACNIC", or "RIPE NCC".
* "recipient_rir" -- (REQUIRED) A string identifying the recipient RIR, with possible values of "AFRINIC", "APNIC",
  "ARIN", "LACNIC", or "RIPE NCC".
* "type" -- (REQUIRED) A string representing the type of transfer, with possible values of "MERGER_ACQUISITION" or
  "RESOURCE_TRANSFER".
* "status" -- (OPTIONAL) A string representing the status of an inter-RIR transfer, with possible values of
  "sourceInitialized", "sourceCancelled", or "sourceFinalized" for the source RIR, and "recipientAccepted" or
  "recipientCancelled" for the recipient RIR.
* "status_date" -- (OPTIONAL) A string representing the date and time when an inter-RIR transfer transitioned to the
  "status" value, per the date-time ABNF rule from [@!RFC3339], with a UTC offset of +00:00, denoted by a time-offset
  ABNF rule value of "Z".

Each transfer object MUST contain only one of the "asns", "ip4nets", or "ip6nets" objects.

An "original_set" object describes the set of resources in the registry from which the related "transfer_set" is taken.
While these sets are often equivalent, the "original_set" can be larger than the "transfer_set". There are transfers in
which the "original_set" is unknown: the receiving RIR may not have knowledge of the "original_set" in the registry of
the source RIR.

The "source_rir" and "recipient_rir" values MUST be identical for an intra-RIR transfer and distinct for an inter-RIR
transfer.

The "status" and "status_date" fields are REQUIRED for an inter-RIR transfer. They provide clarity for resources in
flight between two RIRs, resources when a transfer is cancelled, and a way to detect disputes in resources that are held
by both the RIRs.

The "status" and "status_date" fields MUST NOT be set for an intra-RIR transfer.

The successful path for an inter-RIR transfer would be:

* "sourceInitialized" -> "recipientAccepted" -> "sourceFinalized"

When the source RIR initiates a transfer and the recipient RIR accepts it, the resource may be held in the inventory of
both the RIRs. Once the source RIR issues "sourceFinalized", the resource MUST no longer be in its inventory.

The unsuccessful paths for an inter-RIR transfer would be:

* "sourceInitialized" -> "sourceCancelled"
* "sourceInitialized" -> "recipientAccepted" -> "recipientCancelled"

When the resource is in dispute between two RIRs, the transfer path would be:

* "sourceInitialized" -> "recipientAccepted"

In such a case, there would be no further state change until the matter is resolved. The resource would be held in the
inventory of both the RIRs.

# JSON Schema

The following JSON Schema formally defines the format of a transfer log JSON document:

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
          "const": "4.0"
        },
        "producer": {
          "$ref": "#/$defs/rir_or_nro"
        },
        "UTC_offset": {
          "type": "integer",
          "minimum": -12,
          "maximum": 12
        },
        "production_date": {
          "type": "string",
          "format": "date-time"
        },
        "records_interval": {
          "type": "object",
          "required": [
            "start_date",
            "end_date"
          ],
          "properties": {
            "start_date": {
              "type": "string",
              "format": "date-time"
            },
            "end_date": {
              "type": "string",
              "format": "date-time"
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
        "$ref": "#/$defs/transfer"
      }
    }
  },
  "$defs": {
    "rir": {
      "enum": [
        "AFRINIC",
        "APNIC",
        "ARIN",
        "LACNIC",
        "RIPE NCC"
      ]
    },
    "rir_or_nro": {
      "anyOf": [
        {
          "$ref": "#/$defs/rir"
        },
        {
          "const": "NRO"
        }
      ]
    },
    "organization": {
      "type": "object",
      "properties": {
        "name": {
          "type": "string"
        },
        "country_code": {
          "type": "string",
          "pattern": "^[A-Z]{2}$"
        }
      }
    },
    "asn_set": {
      "type": "array",
      "items": {
        "type": "object",
        "required": [
          "start",
          "end"
        ],
        "properties": {
          "start": {
            "type": "integer",
            "format": "int32"
          },
          "end": {
            "type": "integer",
            "format": "int32"
          }
        }
      }
    },
    "ip4_set": {
      "type": "array",
      "items": {
        "type": "object",
        "required": [
          "start_address",
          "end_address"
        ],
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
            "minItems": 1,
            "items": {
              "type": "object",
              "required": [
                "prefix",
                "length"
              ],
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
      }
    },
    "ip6_set": {
      "type": "array",
      "items": {
        "type": "object",
        "required": [
          "start_address",
          "end_address"
        ],
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
            "minItems": 1,
            "items": {
              "type": "object",
              "required": [
                "prefix",
                "length"
              ],
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
      }
    },
    "transfer": {
      "type": "object",
      "required": [
        "source_rir",
        "recipient_rir",
        "type"
      ],
      "properties": {
        "asns": {
          "type": "object",
          "required": [
            "transfer_set"
          ],
          "properties": {
            "original_set": {
              "$ref": "#/$defs/asn_set"
            },
            "transfer_set": {
              "$ref": "#/$defs/asn_set"
            }
          }
        },
        "ip4nets": {
          "type": "object",
          "required": [
            "transfer_set"
          ],
          "properties": {
            "original_set": {
              "$ref": "#/$defs/ip4_set"
            },
            "transfer_set": {
              "$ref": "#/$defs/ip4_set"
            }
          }
        },
        "ip6nets": {
          "type": "object",
          "required": [
            "transfer_set"
          ],
          "properties": {
            "original_set": {
              "$ref": "#/$defs/ip6_set"
            },
            "transfer_set": {
              "$ref": "#/$defs/ip6_set"
            }
          }
        },
        "source_organization": {
          "$ref": "#/$defs/organization"
        },
        "recipient_organization": {
          "$ref": "#/$defs/organization"
        },
        "transfer_date": {
          "type": "string",
          "format": "date-time"
        },
        "source_registration_date": {
          "type": "string",
          "format": "date-time"
        },
        "source_rir": {
          "$ref": "#/$defs/rir"
        },
        "recipient_rir": {
          "$ref": "#/$defs/rir"
        },
        "type": {
          "enum": [
            "MERGER_ACQUISITION",
            "RESOURCE_TRANSFER"
          ]
        }
      }
    }
  }
}
```

# Example

```
{
  "transfers": [
    {
      "asns": {
        "original_set": [
          {
            "start": 3943,
            "end": 3943
          }
        ],
        "transfer_set": [
          {
            "start": 3943,
            "end": 3943
          }
        ]
      },
      "recipient_organization": {
        "name": "Cisco Systems, Inc.",
        "country_code": "US"
      },
      "recipient_rir": "ARIN",
      "source_organization": {
        "name": "David Meyer/University of Oregon",
        "country_code": "US"
      },
      "source_registration_date": "2001-05-18T04:00:00Z",
      "source_rir": "ARIN",
      "transfer_date": "2012-08-06T17:46:00Z",
      "type": "RESOURCE_TRANSFER"
    },
    {
      "ip6nets": {
        "original_set": [
          {
            "start_address": "2620:001F:8000:0000:0000:0000:0000:0000",
            "end_address": "2620:001F:800F:FFFF:FFFF:FFFF:FFFF:FFFF"
          }
        ],
        "transfer_set": [
          {
            "start_address": "2620:001F:8000:0000:0000:0000:0000:0000",
            "end_address": "2620:001F:8000:FFFF:FFFF:FFFF:FFFF:FFFF"
          }
        ]
      },
      "recipient_organization": {
        "name": "CloudfloorDNS",
        "country_code": "US"
      },
      "recipient_rir": "ARIN",
      "source_organization": {
        "name": "Cloud Floor Corporation",
        "country_code": "US"
      },
      "source_registration_date": "2011-04-19T20:59:54Z",
      "source_rir": "ARIN",
      "transfer_date": "2015-06-22T18:23:56Z",
      "type": "MERGER_ACQUISITION"
    },
    {
      "ip4nets": {
        "original_set": [
          {
            "start_address": "162.208.48.0",
            "end_address": "162.208.51.255"
          }
        ],
        "transfer_set": [
          {
            "start_address": "162.208.48.0",
            "end_address": "162.208.51.255"
          }
        ]
      },
      "recipient_organization": {
        "name": "The AES Corporation",
        "country_code": "US"
      },
      "recipient_rir": "ARIN",
      "source_organization": {
        "name": "Database By Design LLC",
        "country_code": "US"
      },
      "source_registration_date": "2013-04-01T21:13:31Z",
      "source_rir": "ARIN",
      "transfer_date": "2026-04-14T18:59:21Z",
      "type": "RESOURCE_TRANSFER"
    }
  ],
  "version": {
    "UTC_offset": -4,
    "producer": "ARIN",
    "production_date": "2026-04-15T03:59:11Z",
    "records_interval": {
      "start_date": "2009-10-12T18:09:00Z",
      "end_date": "2026-04-14T18:59:21Z"
    },
    "remarks": [
      "Copyright (c) 2026 American Registry for Internet Numbers.",
      "ARIN Terms of Service for this transfers log can be found at https://www.arin.net/tos"
    ],
    "stats_version": "4.0"
  }
}
```

TODO: Use number resources reserved for example documentation.

{backmatter}

