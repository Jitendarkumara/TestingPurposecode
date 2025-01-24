DECLARE @Shift CHAR(1) = 'A'; -- Set the desired shift (A, B, or C)

SELECT *
FROM YourTable
WHERE 
    (@Shift = 'A' AND CAST(DateColumn AS TIME) >= '06:00:00' AND CAST(DateColumn AS TIME) < '14:00:00')
    OR (@Shift = 'B' AND CAST(DateColumn AS TIME) >= '14:00:00' AND CAST(DateColumn AS TIME) < '22:00:00')
    OR (@Shift = 'C' AND (CAST(DateColumn AS TIME) >= '22:00:00' OR CAST(DateColumn AS TIME) < '06:00:00'));