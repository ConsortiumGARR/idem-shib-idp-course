Corso IDEM su Shibboleth Identity Provider del 22-23 Gennaio 2020
--------------------------------------------------------------

#### Docenti
- Giuseppe De Marco (giuseppe.demarco@unical.it)
- Marco Malavolti (marco.malavolti@garr.it)
- Maurizio Festi (maurizio.festi@unitn.it)

#### Playbook setup
````
sudo apt update
sudo apt upgrade -y
sudo apt install -y python3-dev python3-setuptools python3-pip easy-rsa expect-dev git
sudo pip3 install ansible
git clone https://github.com/ConsortiumGARR/ansible-slapd-eduperson2016.git
cd ansible-slapd-eduperson2016/
sudo ansible-playbook -i "localhost," -c local playbook.yml
cd ..
apt install -y python3-pip python-dev libffi-dev libssl-dev libxml2-dev libxslt1-dev libjpeg-dev zlib1g-dev ldap-utils
git clone https://github.com/ConsortiumGARR/Ansible-Shibboleth-IDP-SP-Debian.git
cd Ansible-Shibboleth-IDP-SP-Debian/
ansible-playbook -i "localhost," -c local playbook.yml
````

#### Test
````
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-amazon-corretto/jre
/opt/shibboleth-idp/bin/aacli.sh   -n luigi -r  https://shib-sp.aai-test.garr.it/shibboleth
/opt/shibboleth-idp/bin/aacli.sh   -n luigi -r  https://coco.release-check.edugain.org/shibboleth --saml2
````
# Esempi SAML2

#### Authn Request
````
<samlp:AuthnRequest xmlns:ds="http://www.w3.org/2000/09/xmldsig#"
                    xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
                    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol"
                    AssertionConsumerServiceURL="https://peo.unical.it/saml2/acs/"
                    Destination="https://idp.unical.it/idp/profile/SAML2/POST/SSO"
                    ForceAuthn="true"
                    ID="id-3psTfaaeUPm9D9P6I"
                    IssueInstant="2020-01-16T11:00:32Z"
                    ProtocolBinding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST"
                    Version="2.0"
                    >
    <saml:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity">
      https://sp-fqdn/saml2/metadata/
    </saml:Issuer>
    <ds:Signature Id="Signature1">
        <ds:SignedInfo>
            <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
            <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
            <ds:Reference URI="#id-3psTfaaeUPm9D9P6I">
                <ds:Transforms>
                    <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                    <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
                <ds:DigestValue>TX17iYsk2wSV+tTSF2mL5XIk7LQ=</ds:DigestValue>
            </ds:Reference>
        </ds:SignedInfo>
        <ds:SignatureValue>jnTz7IWSA[...]</ds:SignatureValue>
        <ds:KeyInfo>
            <ds:X509Data>
                <ds:X509Certificate>MIIDPjCCAiYCCQCfoizEvowHTDANBgkqhkiG[...]</ds:X509Certificate>
            </ds:X509Data>
        </ds:KeyInfo>
    </ds:Signature>
    <samlp:NameIDPolicy AllowCreate="true"
                        Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"
                        />
</samlp:AuthnRequest>
````


#### Authn Response
````
<saml2p:Response Destination="https://sp-fqdn/saml2/acs/"
                 ID="_589ef5852730199dcd96d5272446348e"
                 InResponseTo="id-3psTfaaeUPm9D9P6I"
                 IssueInstant="2020-01-16T11:03:30.205Z"
                 Version="2.0"
                 xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol"
                 >
    <saml2:Issuer xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion">https://idp-fqdn/idp/shibboleth</saml2:Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
        <ds:SignedInfo>
            <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
            <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
            <ds:Reference URI="#_589ef5852730199dcd96d5272446348e">
                <ds:Transforms>
                    <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                    <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
                <ds:DigestValue>EOOgR6X6GMPXzWJ5GfsAJAK49/I=</ds:DigestValue>
            </ds:Reference>
        </ds:SignedInfo>
        <ds:SignatureValue>XfQZy87uKZtsH[...]</ds:SignatureValue>
        <ds:KeyInfo>
            <ds:X509Data>
                <ds:X509Certificate>MIIDIDCCAgigAwIBA[...]</ds:X509Certificate>
            </ds:X509Data>
        </ds:KeyInfo>
    </ds:Signature>
    <saml2p:Status>
        <saml2p:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </saml2p:Status>
    <saml2:Assertion ID="_705e108820f05097345daa1116ab503c"
                     IssueInstant="2020-01-16T11:03:30.205Z"
                     Version="2.0"
                     xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion"
                     >
        <saml2:Issuer>https://idp-fqdn/idp/shibboleth</saml2:Issuer>
        <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
                <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
                <ds:Reference URI="#_705e108820f05097345daa1116ab503c">
                    <ds:Transforms>
                        <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                        <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                    </ds:Transforms>
                    <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
                    <ds:DigestValue>ynVwIXWkaS8WhvuPB1dPVqMa1PI=</ds:DigestValue>
                </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>kPqLDBZ8I6eHQwP[...]</ds:SignatureValue>
            <ds:KeyInfo>
                <ds:X509Data>
                    <ds:X509Certificate>MIIDIDCCAgigAwI[...]</ds:X509Certificate>
                </ds:X509Data>
            </ds:KeyInfo>
        </ds:Signature>
        <saml2:Subject>
            <saml2:NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"
                          NameQualifier="https://idp-fqdn/idp/shibboleth"
                          SPNameQualifier="https://sp-fqdn/saml2/metadata/"
                          xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion"
                          >UeMYh+Zf7Y68PGKlkGokCetT0uU=</saml2:NameID>
            <saml2:SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
                <saml2:SubjectConfirmationData Address="160.97.8.193"
                                               InResponseTo="id-3psTfaaeUPm9D9P6I"
                                               NotOnOrAfter="2020-01-16T11:08:30.215Z"
                                               Recipient="https://sp-fqdn/saml2/acs/"
                                               />
            </saml2:SubjectConfirmation>
        </saml2:Subject>
        <saml2:Conditions NotBefore="2020-01-16T11:03:30.205Z"
                          NotOnOrAfter="2020-01-16T11:08:30.205Z"
                          >
            <saml2:AudienceRestriction>
                <saml2:Audience>https://sp-fqdn/saml2/metadata/</saml2:Audience>
            </saml2:AudienceRestriction>
        </saml2:Conditions>
        <saml2:AuthnStatement AuthnInstant="2020-01-16T11:03:30.131Z"
                              SessionIndex="_694a017006ace5eea427f569a7902ed9"
                              >
            <saml2:SubjectLocality Address="10.0.0.1" />
            <saml2:AuthnContext>
                <saml2:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml2:AuthnContextClassRef>
            </saml2:AuthnContext>
        </saml2:AuthnStatement>
        <saml2:AttributeStatement>
            <saml2:Attribute FriendlyName="uid"
                             Name="urn:oid:0.9.2342.19200300.100.1.1"
                             NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"
                             >
                <saml2:AttributeValue>17403</saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="eduPersonScopedAffiliation"
                             Name="urn:oid:1.3.6.1.4.1.5923.1.1.1.9"
                             NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"
                             >
                <saml2:AttributeValue>staff@aai-test.garr.it</saml2:AttributeValue>
                <saml2:AttributeValue>member@aai-test.garr.it</saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="eduPersonAffiliation"
                             Name="urn:oid:1.3.6.1.4.1.5923.1.1.1.1"
                             NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"
                             >
                <saml2:AttributeValue>member</saml2:AttributeValue>
                <saml2:AttributeValue>staff</saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="eduPersonTargetedID"
                             Name="urn:oid:1.3.6.1.4.1.5923.1.1.1.10"
                             NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"
                             >
                <saml2:AttributeValue>
                    <saml2:NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"
                                  NameQualifier="https://idp-fqdn/idp/shibboleth"
                                  SPNameQualifier="https://sp-fqdn/saml2/metadata/"
                                  >UeMYh+Zf7Y68PGKlkGokCetT0uU=</saml2:NameID>
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="sn"
                             Name="urn:oid:2.5.4.4"
                             NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"
                             >
                <saml2:AttributeValue>Rossi</saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="givenName"
                             Name="urn:oid:2.5.4.42"
                             NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"
                             >
                <saml2:AttributeValue>Mario</saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="eduPersonPrincipalName"
                             Name="urn:oid:1.3.6.1.4.1.5923.1.1.1.6"
                             NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"
                             >
                <saml2:AttributeValue>mario@aai-test.garr.it</saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="mail"
                             Name="urn:oid:0.9.2342.19200300.100.1.3"
                             NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"
                             >
                <saml2:AttributeValue>thatuser@thatmailprovider.org</saml2:AttributeValue>
            </saml2:Attribute>
        </saml2:AttributeStatement>
    </saml2:Assertion>
</saml2p:Response>
````
