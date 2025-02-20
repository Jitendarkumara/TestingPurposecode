SELECT TOP 1 
    [timestampSourceLT], 
    ISNULL([Mill1], 0) + ISNULL([Mill2], 0) AS TotalMill, 
    ISNULL([Finishing1], 0) + ISNULL([Finishing2], 0) AS TotalFinishing, 
    [Extcoating]
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
mill (60
61
62
63
65)
Finishing-1  (66
64)
Mill2 (
69
68
71
72
73)
Finishing-2 (70)

Ext-coating -(74
75
76
77)
