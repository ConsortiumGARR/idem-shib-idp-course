# Soluzione Office 365 per IdP V4 e Attribute Registry

1. Nel `relying-party.xml`, sotto a `<util:list id="shibboleth.RelyingPartyOverrides">` aggiungere il seguente `<bean>`:
   ```xml
   <bean id="Office365" parent="RelyingPartyByName" c:relyingPartyIds="urn:federation:MicrosoftOnline">
      <property name="profileConfigurations">
	       <list>
	          <bean parent="SAML2.SSO" p:encryptAssertions="false" p:signAssertions="true" p:signResponses="false" />
            <bean parent="SAML2.ECP" p:encryptAssertions="false" p:signAssertions="true" p:signResponses="false" p:nameIDFormatPrecedence="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent" />
	       </list>
      </property>
   </bean>
   ```

2. All'interno della lista `<util:list id="shibboleth.SAML2NameIDGenerators">` di `saml-nameid.xml` inserire:
   ```xml
   <!-- Uncommenting this bean requires configuration in saml-nameid.properties. -->
   <!--<ref bean="shibboleth.SAML2PersistentGenerator" />-->
	
   <!-- Release Persistent NameID to all but not to MicrosoftOnline-->
   <bean parent="shibboleth.SAML2PersistentGenerator">
      <property name="activationCondition">
         <bean parent="shibboleth.Conditions.NOT">
            <constructor-arg>
               <bean parent="shibboleth.Conditions.RelyingPartyId" c:candidate="urn:federation:MicrosoftOnline" />
            </constructor-arg>
         </bean>
      </property>
   </bean>

   <!-- Microsoft custom Persistent ID Generator -->
   <bean parent="shibboleth.SAML2AttributeSourcedGenerator"
         p:omitQualifiers="true"
         p:format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"
         p:attributeSourceIds="#{ {'ImmutableID'} }">
      <property name="activationCondition">
         <bean parent="shibboleth.Conditions.RelyingPartyId" c:candidate="urn:federation:MicrosoftOnline" />
      </property>
   </bean>
   ```

3. Creare l'`<AttributeDefinition>` per la definizione degli attributi per Office365 (non presente nella directory LDAP) in `attribute-resolver.xml`:
   ```xml
   <!--  Microsoft Office365 - Azure AD ImmutableID & User ID  -->
   <AttributeDefinition xsi:type="Simple" id="ImmutableID">
      <InputDataConnector ref="myLDAP" attributeNames="uid"/>
   </AttributeDefinition>
   
   <AttributeDefinition scope="%{idp.scope}" xsi:type="Scoped" id="UserId">
      <InputDataConnector ref="myLDAP" attributeNames="uid"/>
    </AttributeDefinition>
   ```

4. Creare `/opt/shibboleth-idp/conf/attributes/custom/ImmutableID.properties` in questo modo:
   ```properties
   # Azure AD ImmutableID

   id=ImmutableID
   transcoder=SAML2StringTranscoder
   displayName.en=Azure AD ImmutableID
   displayName.it=Azure AD ImmutableID
   description.en=Azure AD ImmutableID
   description.it=Azure AD ImmutableID
   saml2.name=urn:oid:0.9.2342.19200300.100.1.1
   saml1.encodeType=false
   ```
   
5. Creare `/opt/shibboleth-idp/conf/attributes/custom/UserId.properties` in questo modo:
   ```properties
   # Azure AD User ID

   id=UserId
   transcoder=SAML2ScopedStringTranscoder
   displayName.en=Azure AD User ID
   displayName.it=Azure AD User ID
   description.en=Azure AD User ID
   description.it=Azure AD User ID
   saml2.name=urn:oid:0.9.2342.19200300.100.1.1
   saml1.encodeType=false
   ```

6. Creare i metadata di Office365:
   * `wget https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml -O /opt/shibboleth-idp/metadata/office365-md.xml`
   
   (e rimuovere il NameIDFormat "`unspecified`" o il NameID rilasciato sarà sempre "`transient`")
   
7. Aggiungere i metadata di Office365 a `metadata-providers.xml`:
   ```xml
   <MetadataProvider id="Office365”  xsi:type="FilesystemMetadataProvider" metadataFile="%{idp.home}/metadata/office365-md.xml"/>
   ```
   
8. Configurare il rilascio degli attributi nell'`attribute-filter.xml`:
   ```xml
   <!-- Attribute Filter Policy for Microsoft Office365/Azure -->
   <AttributeFilterPolicy id="PolicyForWindowsAzureAD">
      <PolicyRequirementRule xsi:type="Requester" value="urn:federation:MicrosoftOnline" />

      <!-- Release userPrincipalName as Azure AD User ID -->
      <AttributeRule attributeID="UserId">
         <PermitValueRule xsi:type="ANY"/>
      </AttributeRule>

      <!-- Release Immutable ID to Azure AD -->
      <AttributeRule attributeID="ImmutableID">
         <PermitValueRule xsi:type="ANY"/>
      </AttributeRule>

   </AttributeFilterPolicy>
   ```

9. Provare il rilascio di ImmutableID con AACLI:
   `bash /opt/shibboleth-idp/bin/aacli.sh -n <REPLACE_WITH_USERNAME_IDP> -r urn:federation:MicrosoftOnline --saml2`
