SELECT name AS LoginName,
       is_expiration_checked AS IsPasswordExpirationEnforced,
       is_expired AS IsPasswordExpired
FROM sys.sql_logins
WHERE name = 'YourLoginName';