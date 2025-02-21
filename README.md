DECLARE @SelectedGroup NVARCHAR(50) = 'Mill'; -- Change this to filter by a specific group
DECLARE @StartDate DATETIME = DATEADD(MONTH, -1, GETDATE()); -- 1 month ago
DECLARE @EndDate DATETIME = GETDATE(); -- Current date

SELECT  
    [timestampSourceLT], -- Keep all data
    CASE  
        WHEN [SourceID] IN (64) THEN 'Finishing1'
        WHEN [SourceID] IN (60, 61, 62, 63, 65, 66) THEN 'Mill' 
        WHEN [SourceID] IN (69, 68, 71, 72, 73) THEN 'Mill2' 
        WHEN [SourceID] = 70 THEN 'Finishing2'
        WHEN [SourceID] IN (74, 75, 76, 77) THEN 'Extcoating' 
    END AS GroupName, 
    [Value] -- Keep individual values
FROM [ION_Data].[dbo].[View_DataLog2] 
WHERE QuantityID = 182  
    AND [timestampSourceLT] BETWEEN @StartDate AND @EndDate -- Get only the last 1 month of data
    AND (
        (@SelectedGroup = 'Finishing1' AND SourceID IN (64)) 
        OR (@SelectedGroup = 'Mill' AND SourceID IN (60, 61, 62, 63, 65, 66)) 
        OR (@SelectedGroup = 'Mill2' AND SourceID IN (69, 68, 71, 72, 73)) 
        OR (@SelectedGroup = 'Finishing2' AND SourceID = 70)
        OR (@SelectedGroup = 'Extcoating' AND SourceID IN (74, 75, 76, 77))
    )
ORDER BY [timestampSourceLT] ASC; -- Keep original order