1) Open MySQL/MariaDB.
2) `CREATE USER IF NOT EXISTS 'dbuser'@'localhost' IDENTIFIED BY 'dbuserpw';`
3) `CREATE DATABASE IF NOT EXISTS customDB CHARACTER SET=utf8;`
4) `CREATE TABLE RuoliOrganizzativi(pk int, uid varchar(255), ruolo varchar(255));`
5) `INSERT INTO RuoliOrganizzativi(pk, uid, ruolo) VALUES (1, 'mario', 'tecnico');`
6) `INSERT INTO RuoliOrganizzativi(pk, uid, ruolo) VALUES (2, 'pino', 'amministrativo');`
7) `INSERT INTO RuoliOrganizzativi(pk, uid, ruolo) VALUES (3, 'gino', 'docente');`
8) `GRANT ALL PRIVILEGES ON customDB.* TO 'dbuser'@'localhost';`
9) `FLUSH PRIVILEGES;`

per ottenere:
<pre>
+----+-------+----------------+
| pk | uid   | ruolo          |
+----+-------+----------------+
|  1 | mario | tecnico        |
|  2 | pino  | amministrativo |
|  3 | gino  | docente        |
+----+-------+----------------+
</pre>

10) Add the following "`<bean>`" to "`global.xml`" to be able to use your customDB:

    * `vim /opt/shibboleth-idp/conf/global.xml`
    
      ```xml
      <bean id="MyRDBM"
            class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close" lazy-init="true"
            p:driverClassName="org.mariadb.jdbc.Driver"
            p:url="jdbc:mysql://localhost:3306/customDB?autoReconnect=true"
            p:username="dbuser"
            p:password="dbuserpw"
            p:maxActive="10"
            p:maxIdle="5"
            p:maxWait="15000"
            p:testOnBorrow="true"
            p:validationQuery="select 1"
            p:validationQueryTimeout="5" />
      ```

11) Add the `<AttributeDefinition>` and the `<DataConnector>` needed into the attribute-resolver.xml:

    * `vim /opt/shibboleth-idp/conf/attribute-resolver.xml`
    
      ```xml
      <!-- ...other things ... -->

      <!--  AttributeDefinition for the attribute title  -->

      <AttributeDefinition xsi:type="Simple" id="role">
         <InputDataConnector ref="myDB" attributeNames="userRole" />
      </AttributeDefinition>

      <!-- ... other things... -->

      <!--  Data Connector for customDB  -->

      <DataConnector id="myDB" xsi:type="RelationalDatabase">
         <InputDataConnector ref="myLDAP" attributeNames="uid" />

	      <BeanManagedConnection>MyRDBM</BeanManagedConnection>

            <QueryTemplate>
            <![CDATA[
               select ruolo from RuoliOrganizzativi where (trim(uid)) = trim('$uid[0]')
            ]]>
            </QueryTemplate>
         <Column columnName="ruolo" attributeID="userRole" />
      </DataConnector>
      ```
 
12) Create the custom attribute with:
    * `vim /opt/shibboleth-idp/conf/attributes/custom/role.properties`
    
      ```xml
      # role

      id=role
      transcoder=SAML2StringTranscoder
      displayName.en=user role
      displayName.it=ruolo utente
      description.en=role of the user
      description.it=ruolo dell'utente
      saml2.name=urn:oid:1.2.3.4.5.6.789.10.11.12.13
      saml1.encodeType=false
      ```

13) Add the required Attribute Filter Rule to release the new attribute to your attribute-filter:

    ```xml
    <AttributeRule attributeID="role" permitAny="true" />
    ```

14) The custom attribute is released:

    ```xml
    <saml2:Attribute FriendlyName="role" Name="urn:oid:1.2.3.4.5.6.789.10.11.12.13" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
       <saml2:AttributeValue xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xsd:string">docente</saml2:AttributeValue>
    </saml2:Attribute>
    ```
