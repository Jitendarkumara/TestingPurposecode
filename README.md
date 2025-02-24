ALTER PROCEDURE Proc_GetGraphDataMonthlyWise
    @SelectedGroup NVARCHAR(50)
AS
BEGIN
    SET NOCOUNT ON; -- Prevents extra messages, improving performance

    DECLARE @StartDate DATETIME = DATEADD(MONTH, -1, GETDATE()); -- 1 month ago
    DECLARE @EndDate DATETIME = GETDATE(); -- Current date

    -- Step 1: Use a temporary table to store filtered data
    IF OBJECT_ID('tempdb..#TempData') IS NOT NULL
        DROP TABLE #TempData;

    SELECT  
        timestampSourceLT, 
        SourceID, 
        Value
    INTO #TempData
    FROM [ION_Data].[dbo].[View_DataLog2] WITH (NOLOCK) -- Avoid locking issues
    WHERE QuantityID = 182  
        AND timestampSourceLT BETWEEN @StartDate AND @EndDate; -- Filter before processing

    -- Step 2: Aggregate data using optimized grouping with JOIN
    SELECT  
        t.timestampSourceLT, 
        g.GroupName, 
        SUM(t.Value) AS GroupTotal 
    FROM #TempData t
    JOIN (
        VALUES
            (64, 'Finishing1'),
            (60, 'Mill1'), (61, 'Mill1'), (62, 'Mill1'), (63, 'Mill1'), (65, 'Mill1'), (66, 'Mill1'),
            (69, 'Mill2'), (68, 'Mill2'), (71, 'Mill2'), (72, 'Mill2'), (73, 'Mill2'),
            (70, 'Finishing2'),
            (74, 'Extcoating'), (75, 'Extcoating'), (76, 'Extcoating'), (77, 'Extcoating')
    ) g(SourceID, GroupName) ON t.SourceID = g.SourceID
    WHERE g.GroupName = @SelectedGroup -- Select only the required machine
    GROUP BY t.timestampSourceLT, g.GroupName
    ORDER BY t.timestampSourceLT DESC;

    -- Step 3: Drop temp table to free memory
    DROP TABLE #TempData;
END
GO