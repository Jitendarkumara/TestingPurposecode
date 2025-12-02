Connecting to the database SPML2.
Debugger attempting to connect to database.
Executing PL/SQL: DECLARE
                    id VARCHAR2( 30 );
                  BEGIN
                    id := DBMS_DEBUG.initialize( '127.0.0.1:1764659424269', 0 );
                    DBMS_DEBUG.debug_on( TRUE );
                  END;
Debugger connected to database.
Source breakpoint: P_DELAY_REGISTER.pls:26
Debugger connection to debuggee process has been lost.
ORA-00604: error occurred at recursive SQL level 1
ORA-01013: user requested cancel of current operation
ORA-06510: PL/SQL: unhandled user-defined exception
ORA-00604: error occurred at recursive SQL level 1
ORA-01013: user requested cancel of current operation
ORA-06512: at "SPML2.P_DELAY_REGISTER", line 40
ORA-06512: at line 2
Executing PL/SQL: BEGIN
                    DBMS_DEBUG.debug_off();
                  END;
Process exited.
Disconnecting from the database SPML2.
Debugger disconnected from database.
