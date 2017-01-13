# LDAP Schema for Nextcloud

Reference: https://docs.nextcloud.com/server/11/admin_manual/configuration_user/user_auth_ldap.html

## Nextcloud Schema

OwnCloud Inc. has register the [OID 1.3.6.1.4.1.49213](http://oid-info.com/get/1.3.6.1.4.1.49213) and we extended it to define the required LDAP objects 

- **OID**: 1.3.6.1.4.1.49213.1.2.1
- **ObjectClass**: Nextcloud

### Quota Field

Nextcloud can read an LDAP attribute and set the user quota according to its value. 
The attribute shall return human readable values, e.g. "2 GB".

- **OID**: 1.3.6.1.4.1.49213.1.1.1
- **AttributeType**: NextcloudQuota

#### Quota Field Syntax

The format of the quota field is a string, describing the size of the user quota. You can use the following units (case insensitive) for the size: **B**, **KB**, **K**, **MB**, **M**, **GB**, **G**, **TB**, **T**, **PB**, **P**. If no unit is specified, the default size is in byte (**B**). 

**Valid Value Examples**: "234234", "132594 PB", "748G" 

## Usage

A user can be extended with the auxillary `objectClass: Nextcloud` and the attribute `NextcloudQuota` can be used
to define the user specific quota limit.

## Installation

To install the schema in a OpenLDAP using OLC (cn=config), use the `ldapadd` command:

    root# ldapadd -Y EXTERNAL -H ldapi:/// -f nextcloud.ldif
   
And verify that the schema is correctly loaded:

    root# ldapsearch -H ldapi:// -Y EXTERNAL -LLL -b cn=config "(cn={*}nextcloud)"
    ...
    # {5}nextcloud, schema, config
    dn: cn={5}nextcloud,cn=schema,cn=config
    objectClass: olcSchemaConfig
    cn: {5}nextcloud
    olcAttributeTypes: {0}( 1.3.6.1.4.1.49213.1.1.1 NAME 'NextcloudQuota' DESC 'Use
     r Quota (e.g. 2 GB)' EQUALITY caseExactMatch SUBSTR caseIgnoreSubstringsMatc
     h SINGLE-VALUE SYNTAX 1.3.6.1.4.1.1466.115.121.1.15 )
    olcObjectClasses: {0}( 1.3.6.1.4.1.49213.1.2.1 NAME 'Nextcloud' DESC 'Nextcloud 
      LDAP Schema' AUXILIARY MAY ( NextcloudQuota ) )

If your LDAP server does not use OLC (cn=config), then add the schema `nextcloud.schema` in the schema directory, and update your configuration accordingly.


## Example

    root# ldapsearch -H ldapi:// -Y EXTERNAL -LLL -b ou=Users,dc=apason "(objectclass=nextcloud)" 
    ...
    objectClass: top
    objectClass: person
    objectClass: organizationalPerson
    objectClass: inetOrgPerson
    objectClass: eduMember
    objectClass: Nextcloud
    dn: cn=abraham,ou=Users,dc=apason
    cn: abraham
    sn: Abrahám
    givenName: Miroslav
    mail: miroslav.abraham@apason.cz
    isMemberOf: Nextcloud
    NextcloudQuota: 5 GB


