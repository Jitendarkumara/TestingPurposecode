DECLARE @MillID INT = 65; -- Change this value to select a specific mill (64, 65, 68, 70, or 75)
DECLARE @StartDate DATETIME = DATEADD(MONTH, -1, GETDATE()); -- 1 month ago
DECLARE @EndDate DATETIME = GETDATE(); -- Current date

SELECT TOP 5 
    [timestampSourceLT], 
    CASE 
        WHEN [SourceID] = 64 THEN 'Finishing1'
        WHEN [SourceID] = 65 THEN 'Mill1' 
        WHEN [SourceID] = 68 THEN 'Mill2'  
        WHEN [SourceID] = 70 THEN 'Finishing2'
        WHEN [SourceID] = 75 THEN 'Extcoating' 
    END AS MillName,
    ROUND([Value], 2) AS Valuev
FROM [ION_Data].[dbo].[View_DataLog2] 
WHERE SourceID = @MillID  -- Fetch data only for the selected mill
    AND QuantityID = 182 
    AND [timestampSourceLT] BETWEEN @StartDate AND @EndDate -- Filter for last 1 month
ORDER BY [timestampSourceLT] DESC;