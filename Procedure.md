# Deployment Precedure

## 1. Create Users and Grant Privileges

Each user gets the same password `<password>` and unlimited quota on `TAB_DEF2` (default tablespace).

```sql
-- Create users
create user POT2         identified by "<password>" default tablespace tab_def2 quota unlimited on tab_def2;
create user CRM2         identified by "<password>" default tablespace tab_def2 quota unlimited on tab_def2;
create user CC_REPORT2   identified by "<password>" default tablespace tab_def2 quota unlimited on tab_def2;
create user QMDB2        identified by "<password>" default tablespace tab_def2 quota unlimited on tab_def2;
create user MED2         identified by "<password>" default tablespace tab_def2 quota unlimited on tab_def2;
create user CC2          identified by "<password>" default tablespace tab_def2 quota unlimited on tab_def2;
create user PCC2         identified by "<password>" default tablespace tab_def2 quota unlimited on tab_def2;
create user JNC2         identified by "<password>" default tablespace tab_def2 quota unlimited on tab_def2;
create user APIG2        identified by "<password>" default tablespace tab_def2 quota unlimited on tab_def2;
create user BCSC2        identified by "<password>" default tablespace tab_def2 quota unlimited on tab_def2;
create user BCARE2       identified by "<password>" default tablespace tab_def2 quota unlimited on tab_def2;

-- Grant roles
grant connect, resource, dba to POT2;
grant connect, resource, dba to CRM2;
grant connect, resource, dba to CC_REPORT2;
grant connect, resource, dba to QMDB2;
grant connect, resource, dba to MED2;
grant connect, resource, dba to CC2;
grant connect, resource, dba to PCC2;
grant connect, resource, dba to JNC2;
grant connect, resource, dba to APIG2;
grant connect, resource, dba to BCSC2;
grant connect, resource, dba to BCARE2;
```

## 2. Create Tablespaces
All tablespaces are stored under /ccdata/CC2/. The first set of datafiles is created with autoextend off; additional files are added for the large TAB_CC2 tablespace.

```sql
-- Primary tablespaces (single datafile)
create tablespace tab_def2     datafile '/ccdata/CC2/tab_def_001.dbf' size 30g autoextend off;
create tablespace tab_cc2      datafile '/ccdata/CC2/tab_cc_001.dbf' size 30g autoextend off;
create tablespace idx_cc2      datafile '/ccdata/CC2/idx_cc_001.dbf' size 30g autoextend off;  
create tablespace tab_pcc2     datafile '/ccdata/CC2/tab_pcc_001.dbf' size 30g autoextend off;
create tablespace idx_pcc2     datafile '/ccdata/CC2/idx_pcc_001.dbf' size 30g autoextend off;
create tablespace tab_jnc2     datafile '/ccdata/CC2/tab_jnc_001.dbf' size 30g autoextend off;
create tablespace idx_jnc2     datafile '/ccdata/CC2/idx_jnc_001.dbf' size 30g autoextend off;
create tablespace tab_apig2    datafile '/ccdata/CC2/tab_apig_001.dbf' size 30g autoextend off;
create tablespace idx_apig2    datafile '/ccdata/CC2/idx_apig_001.dbf' size 30g autoextend off;
create tablespace tab_bcsc2    datafile '/ccdata/CC2/tab_bcsc_001.dbf' size 30g autoextend off;
create tablespace idx_bcsc2    datafile '/ccdata/CC2/idx_bcsc_001.dbf' size 30g autoextend off;
create tablespace tab_med2     datafile '/ccdata/CC2/tab_med_001.dbf' size 30g autoextend off;
create tablespace idx_med2     datafile '/ccdata/CC2/idx_med_001.dbf' size 30g autoextend off;
create tablespace tab_mdb2     datafile '/ccdata/CC2/tab_mdb_001.dbf' size 30g autoextend off;
create tablespace idx_mdb2     datafile '/ccdata/CC2/idx_mdb_001.dbf' size 30g autoextend off;
create tablespace tab_ccrpt2   datafile '/ccdata/CC2/tab_ccrpt_001.dbf' size 30g autoextend off;
create tablespace idx_ccrpt2   datafile '/ccdata/CC2/idx_ccrpt_001.dbf' size 30g autoextend off;

-- Add multiple datafiles for TAB_CC2 (16 files of 30 GB = 480 GB)
alter tablespace tab_cc2 add datafile
   '/ccdata/CC2/tab_cc_002.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_003.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_004.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_005.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_006.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_007.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_008.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_009.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_010.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_011.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_012.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_013.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_014.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_015.dbf' size 30g autoextend off,
   '/ccdata/CC2/tab_cc_016.dbf' size 30g autoextend off;
```

# 3. Export Data from Old Environment (Source)
On the source database (where original schemas exist), run the following export as sysdba.
Adjust the DUMP_PATH directory object to a location with sufficient space.

```bash
expdp \"/ as sysdba\" \
  directory=DUMP_PATH \
  LOGFILE=expdp_cc_20260513.log \
  DUMPFILE=expdp_cc_20260513_%U.dmp \
  schemas=POT,CRM,CC_REPORT,QMDB,MED,CC,PCC,JNC,APIG,BCSC,BCARE \
  exclude=STATISTICS \
  parallel=4
```

# 4. Import Data into New Environment (Target)
On the target database (10.26.128.2), copy the dump files to DUMP_PATH and run:
```bash
impdp \"/ as sysdba\" \
  directory=DUMP_PATH \
  dumpfile=expdp_cc_20260513_%U.dmp \
  logfile=impdp_cc_20260513.log \
  parallel=4 \
  remap_schema=POT:POT2,CRM:CRM2,CC_REPORT:CC_REPORT2,QMDB:QMDB2,MED:MED2,CC:CC2,PCC:PCC2,JNC:JNC2,APIG:APIG2,BCSC:BCSC2,BCARE:BCARE2 \
  remap_tablespace=TAB_MIG:TAB_MIG2,TAB_JNC:TAB_JNC2,TAB_APIG:TAB_APIG2,TAB_CCRPT:TAB_CCRPT2,TAB_MDB:TAB_MDB2,IDX_MED:IDX_MED2,IDX_APIG:IDX_APIG2,IDX_JNC:IDX_JNC2,TAB_MED:TAB_MED2,TAB_PCC:TAB_PCC2,IDX_PCC:IDX_PCC2,TAB_DEF:TAB_DEF2 \
  exclude=STATISTICS
```
