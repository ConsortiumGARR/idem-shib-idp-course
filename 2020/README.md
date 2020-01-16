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
    <saml:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity">https://sp-fqdn/saml2/metadata/</saml:Issuer>
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
