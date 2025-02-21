DECLARE @SelectedGroup NVARCHAR(50) = 'Mill1'; -- Change this to filter by a specific group
DECLARE @StartDate DATETIME = DATEADD(MONTH, -1, GETDATE()); -- 1 month ago
DECLARE @EndDate DATETIME = GETDATE(); -- Current date

WITH GroupedData AS (
    SELECT  
        [timestampSourceLT], -- Keep original timestamps
        CASE  
            WHEN [SourceID] = 64 THEN 'Finishing1'
            WHEN [SourceID] IN (60, 61, 62, 63, 65, 66) THEN 'Mill1' 
            WHEN [SourceID] IN (69, 68, 71, 72, 73) THEN 'Mill2' 
            WHEN [SourceID] = 70 THEN 'Finishing2'
            WHEN [SourceID] IN (74, 75, 76, 77) THEN 'Extcoating' 
        END AS GroupName, 
        SUM([Value]) AS GroupTotal -- Sum of all SourceIDs in the same group per timestamp
    FROM [ION_Data].[dbo].[View_DataLog2] 
    WHERE QuantityID = 182  
        AND [timestampSourceLT] BETWEEN @StartDate AND @EndDate -- Get only the last 1 month of data
    GROUP BY  
        [timestampSourceLT], -- Keep all timestamps
        CASE  
            WHEN [SourceID] = 64 THEN 'Finishing1'
            WHEN [SourceID] IN (60, 61, 62, 63, 65, 66) THEN 'Mill1' 
            WHEN [SourceID] IN (69, 68, 71, 72, 73) THEN 'Mill2' 
            WHEN [SourceID] = 70 THEN 'Finishing2'
            WHEN [SourceID] IN (74, 75, 76, 77) THEN 'Extcoating' 
        END
)
SELECT * FROM GroupedData
WHERE GroupName = @SelectedGroup -- Filter by selected machine
ORDER BY timestampSourceLT ASC;