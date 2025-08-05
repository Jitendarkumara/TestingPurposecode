BEGIN
  DBMS_NETWORK_ACL_ADMIN.create_acl (
    acl         => 'debug_acl.xml',
    description => 'ACL for debugging',
    principal   => 'YOUR_USERNAME',
    is_grant    => TRUE,
    privilege   => 'connect'
  );

  DBMS_NETWORK_ACL_ADMIN.assign_acl (
    acl  => 'debug_acl.xml',
    host => '127.0.0.1',
    lower_port => 61535,
    upper_port => 61535
  );
END;
/