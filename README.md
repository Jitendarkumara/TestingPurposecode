SELECT *
FROM (
    SELECT 
        [timestampSourceLT],
        CASE 
            WHEN [SourceID] = 64 THEN 'Mill 1'
            WHEN [SourceID] = 65 THEN 'Mill 2'
            WHEN [SourceID] = 68 THEN 'Mill 3'
            WHEN [SourceID] = 70 THEN 'Mill 4'
            WHEN [SourceID] = 75 THEN 'Mill 5'
        END AS MillName,
        [Value]
    FROM [ION_Data].[dbo].[View_DataLog2]
    WHERE SourceID IN (64, 65, 68, 70, 75) 
      AND QuantityID = 182
) AS SourceData
PIVOT (
    MAX(Value) 
    FOR MillName IN ([Mill 1], [Mill 2], [Mill 3], [Mill 4], [Mill 5])
) AS PivotTable
ORDER BY [timestampSourceLT] DESC;