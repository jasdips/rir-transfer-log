%%%
Title = "NRO Transfer Log"
abbrev = "NRO Transfer Log"
ipr= "trust200902"

[seriesInfo]
name = "Internet-Draft"
value = "nro-transfer-log-00"
stream = "IETF"
status = "standard"
date = 2026-04-15T00:00:00Z

[[author]]
organization="Number Resource Organization"
[author.address]
email = "secretariat@nro.net"

%%%

.# Abstract

This document specifies the format of the JSON file that each RIR produces to publish the number resource transfers
to/from other RIRs in the given period.

{mainmatter}

# Requirements Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",
"NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14
[@!RFC2119] [@!RFC8174] when, and only when, they appear in all capitals, as shown here.

Indentation and whitespace in examples are provided only to illustrate element relationships, and are not a required
feature of this specification.

"..." in examples is used as shorthand for elements defined outside of this document.

# Transfer Log Format {#transfer_log_format}

```
;
; RIR Transfer Log
;
; This is a ruleset for describing the transfers of Autonomous System
; Numbers and IP address networks transacted through Regional Internet
; Registries (RIRs).
;
; This ruleset conforms to JSON Content Rules, defined here:
; https://tools.ietf.org/html/draft-newton-json-content-rules-07
;

# jcr-version 0.7
# ruleset-id rir_xfer_stats

;
; The root of this JSON is a JSON object containing
; version information followed by the transfer information.
;

{ $version , $transfers }



;
; Describes the version of the file.
; This object is a member of the top level object.
;

$version = "version" : {

  ; Denotes version 4.0
  "stats_version"    : "4.0" ,     

  ; Who produced the file: either one of the RIRs or the NRO
  "producer"         : ( $rir | "NRO" ) ,

  ; UTC offset of the producer.
  "UTC_offset"       : -12..12 ,

  ; Date and time the file was produced
  "production_date"  : datetime ,

  ; The period from which the records of this file are covered.
  "records_interval" : { 
    "start_date" : datetime , 
    "end_date"   : datetime 
  } ,

  ; An optional array of strings, where each string contains a semantic distinct
  ; prose (in some contexts, these might be called paragraphs).
  ; This is where a producer would put copyright notices, terms of
  ; service for the data, and production notes.
  "remarks"      : [ string * ] ?

}




;
; Transfers is an array of transfer objects.
;

$transfers = "transfers" : [ $transfer * ]

$transfer = {

  ; The original_set describes the set of resources in the registry from which
  ; the transfer_set is taken. While these sets are often equivalent, the
  ; original_set can be larger than the transfer_set. There are transfers in
  ; which the original_set is unknown, such as transfer to an RIR from another
  ; RIR. That is, the receiving RIR may not have have knowledge of the original_set
  ; in the registry of the source RIR.

  "asns" : {
    "original_set" : $asn_set ? ,
    "transfer_set" : $asn_set
  } ?,

  "ip4nets" : {
    "original_set" : $ip4_set ? ,
    "transfer_set" : $ip4_set
  } ?,

  "ip6nets" : {
    "original_set" : $ip6_set ? ,
    "transfer_set" : $ip6_set
  } ?,

  "source_organization"    : $organization ? ,
  "recipient_organization" : $organization ? ,

  ; date of the transfer
  "transfer_date"            : datetime ? ,

  ; date of the registration of source resources
  ; in the source RIR before the transfer
  "source_registration_date" : datetime ? ,

  "source_rir"    : $rir ,
  "recipient_rir" : $rir ,

  ; The type of transfer.
  "type" : ( "MERGER_ACQUISITION" | "RESOURCE_TRANSFER" )

}



;
; An array of objects, each representing a continguous block
; of autonomous system numbers.

$asn_set = [

  {
    ; The first autonomous system number in the contiguous block.
    "start"               : int32 ,

    ; The last autonomous system number in the contiguous block.
    "end"                 : int32 
  } *

]



;
; An array of objects, each representing a contiguous block of IPv4
; addresses.
;

$ip4_set = [

  {
    ; Start IP address
    "start_address"       : ipv4 ,

    ; End IP address
    "end_address"         : ipv4 ,

    ; An optional array for IPv4 CIDR blocks which make up this
    ; IP block
    "cidrs" : [
      {
        "prefix" : ipv4 ,
        "length" : 0..32
      } +
    ] ?
  } *

]



;
; An array of objects, each representing a contiguous block of IPv6
; addresses.
;

$ip6_set = [

  {
    ; Start IP address
    "start_address"       : ipv6 ,

    ; End IP address
    "end_address"         : ipv6 ,

    ; An optional array for IPv6 CIDR blocks which make up this
    ; IP block
    "cidrs" : [
      {
        "prefix" : ipv6 ,
        "length" : 0..128
      } +
    ] ?
  }

]



;
; The list of the RIRs.
;
$rir = ( "AFRINIC" | "APNIC" | "ARIN" | "LACNIC" | "RIPE NCC" )

;
; Represents an organization that is either the source of the transfer
; or the recipient of the transfer
;
$organization = {

  ; Name of the organization
  "name"                    : string ? ,

  ; 2 letter country code, same as 2.3
  "country_code"            : /[A-Z]{2}/ ?
}
```

The root object contains a "version" object and a "transfers" object.

The "version" object has the following members:
* "stats_version" -- Denotes version 4.0
* "producer" -- Who produced the file: either one of the RIRs or the NRO
* "UTC_offset" -- UTC offset of the producer
* "production_date" -- Date and time the file was produced
* "records_interval" -- The period from which the records of this file are covered
* "remarks" -- An optional array of strings, where each string contains a semantic distinct prose (in some contexts,
  these might be called paragraphs). This is where a producer would put copyright notices, terms of service for the
  data, and production notes.

The "transfers" object is an array of transfer objects. Each transfer object can contain the following members:
* "asns" -- Such an object can contain the following members:
  * "original_set" -- An array of objects, each representing a contiguous block of autonomous system numbers.
  * "transfer_set" -- An array of objects, each representing a contiguous block of autonomous system numbers.
  Both "original_set" and "transfer_set" arrays can contain objects with the following members:
    * "start" -- Start AS number
    * "end" -- End AS number
* "ip4nets" -- Such an object can contain the following members:
  * "original_set" -- An array of objects, each representing a contiguous block of IPv4 addresses.
  * "transfer_set" -- An array of objects, each representing a contiguous block of IPv4 addresses.
    Both "original_set" and "transfer_set" objects can contain the following members:
      * "start_address" -- Start IP address
      * "end_address" -- End IP address
      * "cidrs" -- An optional array for IPv4 CIDR blocks which make up this IP block. Each IPv4 CIDR block object in
        this array can contain the following members:
        * "prefix"
        * "length" -- 0 to 32
* "ip6nets" -- Such an object can contain the following members:
  * "original_set" -- An array of objects, each representing a contiguous block of IPv6 addresses.
  * "transfer_set" -- An array of objects, each representing a contiguous block of IPv6 addresses.
    Both "original_set" and "transfer_set" objects can contain the following members:
      * "start_address" -- Start IP address
      * "end_address" -- End IP address
      * "cidrs" -- An optional array for IPv6 CIDR blocks which make up this IP block. Each IPv6 CIDR block object in
        this array can contain the following members:
          * "prefix"
          * "length" -- 0 to 128
* "source_organization" -- An object that represents an organization that is the source of the transfer.
* "recipient_organization" -- An object that represents an organization that is the recipient of the transfer.
Both "source_organization" and "recipient_organization" objects can contain the following members:
  * "name" -- Name of the organization
  * "country_code" -- 2-letter country code
* "transfer_date" -- Date of the transfer
* "source_registration_date" -- Date of the registration of source resources in the source RIR before the transfer
* "source_rir" -- Source RIR
* "recipient_rir" -- Recipient RIR
* "type" -- The type of transfer
* "status" -- Status of the transfer
* "status_date" -- Date when the transfer transitioned to the "status" value

Each transfer object can contain only one of the "asns", "ip4nets", or "ip6nets" objects.

## Example

```
{
  "transfers" : [ {
    "asns" : {
      "original_set" : [ {
        "start" : 3943,
        "end" : 3943
      } ],
      "transfer_set" : [ {
        "start" : 3943,
        "end" : 3943
      } ]
    },
    "recipient_organization" : {
      "name" : "Cisco Systems, Inc.",
      "country_code" : "US"
    },
    "recipient_rir" : "ARIN",
    "source_organization" : {
      "name" : "David Meyer/University of Oregon",
      "country_code" : "US"
    },
    "source_registration_date" : "2001-05-18T04:00:00Z",
    "source_rir" : "ARIN",
    "transfer_date" : "2012-08-06T17:46:00Z",
    "type" : "RESOURCE_TRANSFER"
  }, {
    "ip6nets" : {
      "original_set" : [ {
        "start_address" : "2620:001F:8000:0000:0000:0000:0000:0000",
        "end_address" : "2620:001F:800F:FFFF:FFFF:FFFF:FFFF:FFFF"
      } ],
      "transfer_set" : [ {
        "start_address" : "2620:001F:8000:0000:0000:0000:0000:0000",
        "end_address" : "2620:001F:8000:FFFF:FFFF:FFFF:FFFF:FFFF"
      } ]
    },
    "recipient_organization" : {
      "name" : "CloudfloorDNS",
      "country_code" : "US"
    },
    "recipient_rir" : "ARIN",
    "source_organization" : {
      "name" : "Cloud Floor Corporation",
      "country_code" : "US"
    },
    "source_registration_date" : "2011-04-19T20:59:54Z",
    "source_rir" : "ARIN",
    "transfer_date" : "2015-06-22T18:23:56Z",
    "type" : "MERGER_ACQUISITION"
  }, {
    "ip4nets" : {
      "original_set" : [ {
        "start_address" : "162.208.048.000",
        "end_address" : "162.208.051.255"
      } ],
      "transfer_set" : [ {
        "start_address" : "162.208.048.000",
        "end_address" : "162.208.051.255"
      } ]
    },
    "recipient_organization" : {
      "name" : "The AES Corporation",
      "country_code" : "US"
    },
    "recipient_rir" : "ARIN",
    "source_organization" : {
      "name" : "Database By Design LLC",
      "country_code" : "US"
    },
    "source_registration_date" : "2013-04-01T21:13:31Z",
    "source_rir" : "ARIN",
    "transfer_date" : "2026-04-14T18:59:21Z",
    "type" : "RESOURCE_TRANSFER"
  } ],
  "version" : {
    "UTC_offset" : -4,
    "producer" : "ARIN",
    "production_date" : "2026-04-15T03:59:11Z",
    "records_interval" : {
      "start_date" : "2009-10-12T18:09:00Z",
      "end_date" : "2026-04-14T18:59:21Z"
    },
    "remarks" : [ "Copyright (c) 2026 American Registry for Internet Numbers.", "ARIN Terms of Service for this transfers log can be found at https://www.arin.net/tos" ],
    "stats_version" : "4.0"
  }
} 
```

TODO: Use number resources reserved for example documentation.

{backmatter}

