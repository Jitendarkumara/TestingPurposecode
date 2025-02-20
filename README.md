SELECT 
    [timestampSourceLT], 
    CASE  
        WHEN [SourceID] = 64 THEN 'Finishing1'
        WHEN [SourceID] = 65 THEN 'Mill1' 
        WHEN [SourceID] = 68 THEN 'Mill2' 
        WHEN [SourceID] = 70 THEN 'Finishing2'
        WHEN [SourceID] = 75 THEN 'Extcoating' 
    END AS MillName, 
    ROUND([Value], 2) AS Value
FROM [ION_Data].[dbo].[View_DataLog2] 
WHERE SourceID IN (64, 65, 68, 70, 75) 
    AND QuantityID = 182
ORDER BY [timestampSourceLT] DESC, MillName;