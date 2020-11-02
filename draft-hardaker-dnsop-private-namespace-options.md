---
title: "DNS Private Namespace Options"
abbrev: DNS Private Namespace Options
docname: draft-hardaker-dnsop-private-namespace-options-00
category: std
ipr: trust200902

stand_alone: yes
pi: [toc, sortrefs, symrefs, docmapping]

author:
  -
    ins: W. Hardaker
    name: Wes Hardaker
    org: USC/ISI
    email: ietf@hardakers.net

normative:
 
informative:

--- abstract

This document discusses the trade-offs between various options about
creating a private namespace within top level domains within the root
zone.

--- middle

# Introduction

Deployed DNS clients within the Internet typically communicate with
upstream resolvers using their own in-application stub resolver.
These upstream resolvers may be run by ISPs, or may be
a customer-premises equipment (CPE) that may or may not forward
requests to its upstream ISP.

In an entirely singular Internet DNS there would be no name collisions
as all data is uniquely named.  However, the prevalence of local
private name spaces within companies, organizations, governments, home
LANs, etc have shown that existence of a single, unique naming system
rarely exists.  The deployment of Internet of Things (IoT) devices is
only accelerating this trend for private namespaces by devices that
bootstrap their names with the easy solution of "just make one up
until the customer provides us with a better one", followed by the
customer never providing one.  This document makes no judgment on
whether this is right or wrong, and takes this assumption as simply
the state of the current world.

The for special use names is well spelled out in {{?RFC6761}}.
{{?RFC8244}} provides additional insight into areas that are still
under discussion and where work is needed.  Recently ICANN's SSAC has
issued [SAC113] entitled "SSAC Advisory on Private-Use TLDs", wherein
it suggests the creation of a private-use DNS TLD.

This document considers the aspects associated with DNSSEC and the
potential choices for a private-use TLD (also see {{?RFC8244}} bullet
21).  Specifically, we consider the case where somewhere in the
resolution path DNSSEC validation is in use, potentially at an
end-device (phone, laptop, etc), a CPE, or at an ISP's resolver.

# Analysis of choices

## Assumptions

We make the following assumptions to begin:

1. A local environment needs to use both the Global Internet's DNS
   (GID), as well as at least one private name space as well.
1. A end-device, a CPE and/or a resolver may choose to validate DNS requests.
1. The validating resolver wishes to validate both responses from the
   GID as well as local names using DNSSEC.
1. The validating resolver will, thus, need Trust Anchors (TAs) for
   both the GID and all private namespaces, or will need a list of
   names which are assumed insecure and exemptions to the GID. 
1. The device may (unfortunately) move to another network where 
   private namespace resolution is not available, and thus queries to
   it will leak to the GID.  This is extremely common today.
1. We take as accepted consensus that anything protocol needing a
   private name space that is not user visible can be properly housed
   under .arpa.  This document assumes a private-namespace TLD is
   needed, as discussed in other documents ([SAC113, etc]) to aid in
   user presentation and understanding.  This document does not make
   judgment on whether this or user-education may be the right
   approach to this problem.

## TLD choices

Given these assumptions, we consider the cases where a private
namespace TLD exists that is:

1. Is a special-use domain per {{?RFC6761}}, and does not (and will
   never) exist in the GID.  In this document, we refer to this as
   ".internal" for discussion purposes only following conventions in
   [draft-wkumari-dnsop-internal].
2. Is an unsigned delegation within the (GID's) DNS root, with NS
   records likely pointing eventually to something like 127.0.53.53.
   In this document, we refer to this as ".zz" following convention in
   [draft-ietf-dnsop-private-use-tld].
   
In summary:

- .internal is an unsigned TLD
- .zz is a special-use like TLD that MUST never be assigned

### Analysis of an unsigned TLD (aka .internal)

An unsigned TLD such as .internal will:

- Exist within the DNS root
- have NS records pointing to something.arpa with on A/AAAA resolution

#### non-validating end-devices querying within .internal will:

- inside the private network will:
    - Believe the upstream resolver's responses
- outside the private network will:
    - Believe the upstream resolver's NXDOMAIN responses

#### validating end-devices querying within .internal will:

- inside the private network will:
    - must be configured with a private TA to enable DNSSEC within
      the private network (creating an island of trust)
    - If unconfigured, it will believe the upstream resolver's
      responses because its delegated insecure, and therefore has
      no basis to distrust the answers
- outside the private network will:
    - if not configured with a TA, all answers to .internal will
      either be NXDOMAIN or spoofable
    - if configured with a TA, all answers will be detected as
      BOGUS

### Analysis of a special-use TLD (aka .zz)

A special-use TLD will:

- Not exist within the DNS root
- Proven by the root's NSEC chain



# Recommendation

## Requirements notation

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in {{?RFC2119}}

# Contrasting Proposals {#anothersection}

# Differences in security fundamentals

~~~
TLD1 86400 IN NS 127.0.0.1
~~~

# Security Considerations

[TBD]

# IANA Considerations


--- back

# Acknowledgments

Large portions of the technical analysis in this document derives
from a discussion with Roy Arends and Warren Kumari (back when we
could stand in front of a whiteboard together).