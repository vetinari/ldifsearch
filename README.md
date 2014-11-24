# NAME

ldifsearch - filter entries from an LDIF file

# SYNOPSIS

    ldifsearch -L LDIF [-S SCHEMA_LDIF] \
           [OPTIONS] [FILTER [ATTR [ATTR [ ... ]]]]

    ldifsearch -L LDIF [-S SCHEMA_LDIF] \
           [OPTIONS] ldap:///SEARCH_BASE?SCOPE?FILTER?ATTR,ATTR

# DESCRIPTION

ldifsearch searches an LDIF file with the LDAP filter FILTER, similar to
[ldapsearch(1)](http://man.he.net/man1/ldapsearch) and writes all matching entries as LDIF to stdout, when
ATTRs are also given, only the DN and the given attributes (if present).

# NOTES

This script requires at least version 0.50 of the
[Net::LDAP](https://github.com/perl-ldap/perl-ldap) distribution
(i.e. Net::LDAP::FilterMatch version 0.23) and Net::LDAP::DN from
[https://github.com/vetinari/perl-net-ldap-dn](https://github.com/vetinari/perl-net-ldap-dn).

# OPTIONS

- \--ldif=LDIF
- \-L LDIF

The LDIF file to use.

- \--schema=LDIF
- \-S LDIF

The schema LDIF file to use for matching rules, see [Net::LDAP::FilterMatch](http://search.cpan.org/perldoc?Net::LDAP::FilterMatch).
To retrieve the schema for your LDAP server use:

    perl -MNet::LDAP -MNet::LDAP::Schema \
       -e 'Net::LDAP->new(shift)->schema()->dump()' $LDAP_URI > schema.ldif

- \--base=BASE
- \-b BASE

Search base for queries, if not given, searches all entries in the LDIF
file.

- \--scope=SCOPE
- \-s SCOPE

The search scope. One of `sub`, `base` or `one`, defaults to `sub`.

- \--attrs-only
- \-A

Only print attributes

- \--ref OC,ATTR,REF\_ATTR
- \-r OC,ATTR,REF\_ATTR

Add references for objectClass OC: the DN of the object will be added as
attribute REF\_ATTR for all DNs referenced by the value of ATTR. Example:

All _member_ of a _groupOfNames_ get a _memberOf_ attribute with the
DN of the _groupOfNames_ as value.

This option is usually not needed when the LDIF file is the result of
an ldapsearch with `* +` as attribute arguments, the (operational)
_memberOf_ attribute should be present when your LDAP server supports
it. For OpenLDAP's slapd the _memberof_ module must be loaded (...
before __any__ _member_ attribute is set) to get the _memberOf_
attribute. For an LDIF produced by slapcat, this must be used to query
the _memberOf_ (or alike referenced attributes/values).

__NOTE__: dynamic groups like _groupOfURLs_ are currenly not supported.

- \--memberof
- \-m

Shortcut for `--ref=groupOfNames,member,memberOf`

- \--dynlist OC,ATTR
- \--dynlist OC,ATTR,REF\_ATTR,REF\_REF
- \-D OC,ATTR
- \-D OC,ATTR,REF\_ATTR,REF\_REF

Add dynamic list support like [slapo-dynlist(5)](http://man.he.net/man5/slapo-dynlist). When called with four
arguments it will build the REF\_REF attribute in a second run. Example: 
`--dynlist=groupOfURLs,labeledURI,member,memberOf` will resolve the 
query given in the _labeledURI_ in to the _member_ attribute which is
then resolved to the _memberOf_ attribute (see above for ["--ref"](#--ref)).

- \--dyngroup
- \-d

Shortcut for `--dynlist=groupOfURLs,labeledURI,member,memberOf`

- \--opt K=V
- \-o K=V

General options.

Only known key is "ldif-wrap", value: "no" or a number, see [Net::LDAP::LDIF](http://search.cpan.org/perldoc?Net::LDAP::LDIF)
about wrapping long lines

- \--sizelimit=INT
- \-z INT

Return max INT matches.

# SEE ALSO

[Net::LDAP](http://search.cpan.org/perldoc?Net::LDAP), [Net::LDAP::LDIF](http://search.cpan.org/perldoc?Net::LDAP::LDIF), [ldapsearch(1)](http://man.he.net/man1/ldapsearch), [Net::LDAP::DN](http://search.cpan.org/perldoc?Net::LDAP::DN)

# AUTHOR

Hanno Hecker <vetinari@ankh-morp.org>
