BEGIN
  DBMS_NETWORK_ACL_ADMIN.APPEND_HOST_ACE(
    host       => '127.0.0.1',
    lower_port => 61535,
    upper_port => 61535,
    ace        => xs$ace_type(
                   privilege => 'connect',
                   principal => 'YOUR_USERNAME',
                   principal_type => 'USER'
                 )
  );
END;
/