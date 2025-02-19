SELECT 
    p.timestampSourceLT, 
    'Mill 1' AS [64], p.[64],
    'Mill 2' AS [65], p.[65],
    'Mill 3' AS [68], p.[68],
    'Mill 4' AS [70], p.[70],
    'Mill 5' AS [75], p.[75]
FROM (
    SELECT 
        [timestampSourceLT],
        [SourceID],
        [Value]
    FROM [ION_Data].[dbo].[View_DataLog2]
    WHERE SourceID IN (64, 65, 68, 70, 75) 
      AND QuantityID = 182
) AS SourceData
PIVOT (
    MAX(Value) 
    FOR SourceID IN ([64], [65], [68], [70], [75])
) AS p
ORDER BY p.[timestampSourceLT] DESC;