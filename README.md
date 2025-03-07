ALTER PROCEDURE [dbo].[Proc_GetMonthlyEms_Model_data]
    @mill NVARCHAR(20),
    @feeder NVARCHAR(20)
AS
BEGIN
    SET NOCOUNT ON;

    IF (@feeder = 'All')
    BEGIN
        SELECT TOP (100) 
            [Timestamp], 
            Mill_Id, 
            SUM(Act_value) AS TotalActValue
        FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output]
        WHERE Mill_Id = @mill
        GROUP BY [Timestamp], Mill_Id
        ORDER BY [Timestamp] DESC;
    END
    ELSE
    BEGIN
        SELECT TOP (100) 
            [Timestamp], 
            Mill_Id, 
            [Parameter], 
            SUM(Act_value) AS TotalActValue
        FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output]
        WHERE Mill_Id = @mill AND [Parameter] = @feeder
        GROUP BY [Timestamp], Mill_Id, [Parameter]
        ORDER BY [Timestamp] DESC;
    END
END;