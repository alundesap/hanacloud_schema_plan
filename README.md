HANA Cloud Schema Plan Application

![SAP HANA Cloud Getting Started](/images/137441E1-671B-4D44-B371-BC765CBA5D22.png)

Build Command:
```
cd hanacloud_schema_plan ; mkdir -p mta_archives ; mbt build -p=cf -t=mta_archives --mtar=hanacloud_schema_plan.mtar
```

Deploy Command:
```
cf deploy mta_archives/hanacloud_schema_plan.mtar -f
```

Undeploy Command:
```
cf undeploy hcsp -f --delete-services
```
