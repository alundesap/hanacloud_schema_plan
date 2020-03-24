### HANA Cloud Schema Plan Application

If you don't have one already, create a SAP HANA Cloud Instance following this section of the documentation and then return to this README.md.

https://help.sap.com/viewer/db19c7071e5f4101837e23f06e576495/cloud/en-US/784a1dbb421a4da29fb1e3bdf5f198ec.html

![SAP HANA Cloud Getting Started](/images/137441E1-671B-4D44-B371-BC765CBA5D22.png)

Once your HANA Cloud Instance has been created, you will need to get its internal identifier.  This can be found on the SAP HANA Cloud Instances panel as the first part of the Endpoint.  Note this may not always be the case and you should verify it by using the cf cli tool as described below.

![SAP Cloud Platform Cockpit](/images/64F1AF48-4038-450F-BD09-83A577B3C872.png)

Run the following commands in a terminal on your workstation if you've installed them locally or in the Business Application Studio if your local workstation doesn't have a linux-like environment.  [Link to Business Application Studio](https://community.sap.com/topics/business-application-studio).  Substitute **<hana_cloud_name>** for yours.

```
cf services | grep hana-cloud
```
**<hana_cloud_name>**    hana-cloud        hana                                    create succeeded


Now that we have the hana cloud instance name, we need to find it's guid.
```
cf service <hana_cloud_name> --guid
```
9bf5f11a-394c-459e-b251-ff99ea73a3dc < Yours will be different. We will call this **<hana_cloud_guid>**

Now create a HANA service instance with plan "schema" that will provide the binding a future application can use to connect to the HANA Cloud instance.

Here we will use the same name as would be created if you deploy this sample application( HCSP_SCH). 

> Note: the "schema" property is optional.  If you remove it, the system will create a uniquely named schema for you.

```
cf create-service hana schema  HCSP_SCH -c '{"schema": " HCSP_DB", "database_id": "**<hana_cloud_guid>**"}'
```

> Note: The creation is done in the background.  You can use "cf s" to check on its progress.

Once the hana schema service instance has been created, you need to create a service key for it in order to obtain the credential details.

```
cf create-service-key HCSP_SCH SCH_key
```

In order to show the key details use the "service-key" command.

```
cf service-key HCSP_SCH SCH_key
```

The details of host, port, schema, user, and password as well as certificate are shown.  HANA Cloud requires that connections be encrypted.  An example of how to make a connection to the HANA Cloud instance it included in the python code example.

```
# In python/server.py

        connection = dbapi.connect(
            address=host,
            port=int(port),
            user=user,
            password=password,
            currentSchema=schema,
            encrypt="true",
            sslValidateCertificate="true",
            sslCryptoProvider="openssl",
            sslTrustStore=haascert
        )
```

### Build Command:
```
cd hanacloud_schema_plan ; mkdir -p mta_archives ; mbt build -p=cf -t=mta_archives --mtar=hanacloud_schema_plan.mtar
```

### Deploy Command:
```
cf deploy mta_archives/hanacloud_schema_plan.mtar -f
```

### Undeploy Command:
```
cf undeploy hcsp -f --delete-services
```
