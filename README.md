SELECT name AS LoginName,
       is_expiration_checked AS IsPasswordExpirationEnforced
FROM sys.sql_logins
WHERE name = 'YourLoginName';