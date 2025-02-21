SELECT  
        [timestampSourceLT], 
        CASE  
            WHEN [SourceID] IN ( 64) THEN 'Finishing1'
            WHEN [SourceID] IN (60, 61, 62, 63, 65,66) THEN 'Mill' 
            WHEN [SourceID] IN (69, 68, 71, 72, 73) THEN 'Mill2' 
            WHEN [SourceID] = 70 THEN 'Finishing2'
            WHEN [SourceID] IN (74, 75, 76, 77) THEN 'Extcoating' 
        END AS GroupName, 
        [Value] 
    FROM [ION_Data].[dbo].[View_DataLog2] 
    WHERE SourceID IN (60, 61, 62, 63, 65, 66, 64, 69, 68, 71, 72, 73, 70, 74, 75, 76, 77) 
        AND QuantityID = 182  ORDER BY [timestampSourceLT] DESC
