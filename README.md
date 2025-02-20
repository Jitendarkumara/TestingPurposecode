SELECT TOP 1 
    [timestampSourceLT], 
    ROUND(ISNULL([Mill1], 0), 2) AS Mill1, 
    ROUND(ISNULL([Mill2], 0), 2) AS Mill2, 
    ROUND(ISNULL([Finishing1], 0), 2) AS Finishing1, 
    ROUND(ISNULL([Finishing2], 0), 2) AS Finishing2, 
    ROUND(ISNULL([Extcoating], 0), 2) AS Extcoating
FROM 
    (SELECT  
        [timestampSourceLT], 
        CASE  
            WHEN [SourceID] = 64 THEN 'Finishing1'
            WHEN [SourceID] = 65 THEN 'Mill1' 
            WHEN [SourceID] = 68 THEN 'Mill2' 
            WHEN [SourceID] = 70 THEN 'Finishing2'
            WHEN [SourceID] = 75 THEN 'Extcoating' 
        END AS MillName, 
        [Value] 
    FROM [ION_Data].[dbo].[View_DataLog2] 
    WHERE SourceID IN (64, 65, 68, 70, 75) 
        AND QuantityID = 182
    ) AS SourceData 
PIVOT 
    (MAX(Value) FOR MillName IN ([Finishing1], [Mill1], [Mill2], [Finishing2], [Extcoating])) AS PivotTable 
ORDER BY [timestampSourceLT] DESC;
