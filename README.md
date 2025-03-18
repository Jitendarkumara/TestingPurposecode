ALTER PROCEDURE [dbo].[Proc_GetMonthlyEms_Model_data]
    @mill NVARCHAR(50),
    @feeder NVARCHAR(200)
AS
BEGIN
    SET NOCOUNT ON;

    IF (@feeder = 'All')
    BEGIN
        SELECT  
            [Timestamp], 
            Mill_Id, 
            SUM(Act_value) AS TotalActValue
        FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output]
        WHERE Mill_Id = @mill
        AND [Timestamp] >= DATEADD(MONTH, -1, GETDATE())  -- Last 1 month data
        GROUP BY [Timestamp], Mill_Id
        ORDER BY [Timestamp] DESC;
    END
    ELSE
    BEGIN
        SELECT  
            [Timestamp], 
            Mill_Id, 
            [Parameter], 
            SUM(ISNULL(Act_value, 0)) AS TotalActValue
        FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output]
        WHERE Mill_Id = @mill 
        AND [Parameter] = @feeder
        AND [Timestamp] >= DATEADD(MONTH, -1, GETDATE())  -- Last 1 month data
        GROUP BY [Timestamp], Mill_Id, [Parameter]
        ORDER BY [Timestamp] DESC;
    END
END;