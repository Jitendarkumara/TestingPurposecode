SELECT *
FROM (
    SELECT [Timestamp], Mill_Id, TotalActvalue
    FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output]
) AS SourceTable
PIVOT (
    MAX(TotalActvalue) 
    FOR Mill_Id IN ([Mill1], [Mill2], [Mill3], [Mill4])  -- Add all necessary Mill_Id values
) AS PivotTable
ORDER BY [Timestamp] DESC;