#### Net Core Connection String for Oracle platform independent
put this in appsettings.json 
 "ConnectionString": "User Id=[userid];Password=[password];Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST =
 [hostname])(PORT = 1521))(CONNECT_DATA=(SERVICE_NAME=[dbname])));"


