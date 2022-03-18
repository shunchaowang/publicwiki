#### Net Core Connection String for Oracle platform independent
put this in appsettings.json 
 "ConnectionString": "User Id=[userid];Password=[password];Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST =
 [hostname])(PORT = 1521))(CONNECT_DATA=(SERVICE_NAME=[dbname])));"
#### check Oracle Database Locks
```
select * from ORDHEAD_CFA_EXT where order_no=10419992 and group_id=83;
-- revert the record pulled to return on next query
update ordhead_cfa_ext set varchar2_1='Y' where order_no=10419992 and group_id=83;
-- check db locks
SELECT  l.inst_id,
        SUBSTR(L.ORACLE_USERNAME,1,8) ORA_USER,
        SUBSTR(L.SESSION_ID,1,3) SID,
        S.serial#,
        SUBSTR(O.OWNER||'.'||O.OBJECT_NAME,1,40) OBJECT, P.SPID OS_PID,
        DECODE(L.LOCKED_MODE,   0,'NONE',
               1,'NULL',
               2,'ROW SHARE',
               3,'ROW EXCLUSIVE',
               4,'SHARE',
               5,'SHARE ROW EXCLUSIVE',
               6,'EXCLUSIVE',
               NULL) LOCK_MODE
FROM    sys.GV_$LOCKED_OBJECT L
   , DBA_OBJECTS O
   , sys.GV_$SESSION S
   , sys.GV_$PROCESS P
WHERE     L.OBJECT_ID = O.OBJECT_ID
  and     l.inst_id = s.inst_id
  AND     L.SESSION_ID = S.SID
  and     s.inst_id = p.inst_id
  AND     S.PADDR = P.ADDR(+)
order by l.inst_id;

-- after getting the sid and serial#, can kill the specific lock
ALTER SYSTEM KILL SESSION 'sid,serial#' IMMEDIATE;
```

