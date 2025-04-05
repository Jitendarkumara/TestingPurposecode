For Killing Sessions:
1. Open cmd 
2. sqlplus / as sysdba
3. -- exceute this command--
    set pages 200 lines 300	
4. select username,module,terminal,status,count(1) from v$session where type<>'BACKGROUND' group by username,module,terminal,status order by 5 desc;
5. --kill session--
    select 'alter system kill session '||''''||sid||','||serial#||''' immediate;' from v$session where type <> 'BACKGROUND' and status in ('INACTIVE','SNIPED') and last_call_et > 800;

   
