# Soluzione Google Suite per IdP V4 e Attribute Registry

1. Nel `relying-party.xml`, sotto a `<util:list id="shibboleth.RelyingPartyOverrides">` aggiungere il seguente `<bean>`:
   ```xml
   <bean id="Google" parent="RelyingPartyByName" c:relyingPartyIds="google.com">
      <property name="profileConfigurations">
	       <list>
	          <bean parent="SAML2.SSO" p:encryptAssertions="false" p:encryptNameIDs="false" />
	       </list>
      </property>
   </bean>
   ```

2. All'interno della lista `<util:list id="shibboleth.SAML2NameIDGenerators">` di `saml-nameid.xml` inserire:
   ```xml
   <!-- Release to Google an "emailAddress" NameID with the value of Gprincipal -->
   <bean parent="shibboleth.SAML2AttributeSourcedGenerator"
         p:format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress"
         p:attributeSourceIds="#{ {'Gprincipal'} }">
      <property name="activationCondition">
         <bean parent="shibboleth.Conditions.RelyingPartyId" c:candidate="google.com" />
      </property>
   </bean>
   ```

3. Creare l'`<AttributeDefinition>` per la generazione della mail di Google (se non presente nella directory LDAP)
   ```xml
   <AttributeDefinition xsi:type="Template" id="Gprincipal">
      <InputDataConnector ref="myLDAP" attributeNames="uid" />
      <Template>
         <![CDATA[
            ${uid}@gmail.com
         ]]>
      </Template>
   </AttributeDefinition>
   ```

4. Creare `/opt/shibboleth-idp/conf/attributes/custom/Gprincipal.properties` in questo modo:
   ```properties
   # Gprincipal

   id=Gprincipal
   transcoder=SAML2StringTranscoder
   displayName.en=Username Google
   displayName.it=Username Google
   description.en=Username for Google
   description.it=Username usato da Google
   saml2.name=urn:oid:0.9.2342.19200300.100.1.3
   saml1.encodeType=false
   ```

5. Creare i metadata di Google in `/opt/shibboleth-idp/metadata/google-md.xml` stando attenti a sostituire "`university.edu`" con il valore corretto per la propria istituzione:
   ```xml
   <?xml version="1.0" encoding="UTF-8" standalone="no"?>

   <!-- entityID = "google.com" -->
   <EntityDescriptor entityID="google.com" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
      <SPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
         <NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</NameIDFormat>
         <AssertionConsumerService index="1" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://www.google.com/a/university.edu/acs" />
      </SPSSODescriptor>
   </EntityDescriptor>
   ```
   
6. Aggiungere i metadata di Google a `metadata-providers.xml`:
   ```xml
   <MetadataProvider id="Google”  xsi:type="FilesystemMetadataProvider" metadataFile="%{idp.home}/metadata/google-md.xml"/>
   ```
   
7. Configurare il rilascio del `Gprincipal` nell'`attribute-filter.xml`:
   ```xml
   <!-- G Suite (Google Apps)  -->
   <AttributeFilterPolicy id="google.com">
      <PolicyRequirementRule xsi:type="Requester" value="google.com" />
      <AttributeRule attributeID="Gprincipal">
         <PermitValueRule xsi:type="ANY" />
      </AttributeRule>
   </AttributeFilterPolicy>
   ```

8. Provare il rilascio con AACLI:
   `bash /opt/shibboleth-idp/bin/aacli.sh -n <REPLACE_WITH_USERNAME_IDP> -r google.com --saml2`
