Connecting to the database SPML2.
Debugger attempting to connect to database.
Executing PL/SQL: DECLARE
                    id VARCHAR2( 30 );
                  BEGIN
                    id := DBMS_DEBUG.initialize( '127.0.0.1:1764664048818', 0 );
                    DBMS_DEBUG.debug_on( TRUE );
                  END;
Debugger connected to database.
Source breakpoint: P_DELAY_REGISTER_TEST.pls:15
Executing PL/SQL: BEGIN
                    DBMS_DEBUG.debug_off();
                  END;
Debugger connection to debuggee process has been lost.
