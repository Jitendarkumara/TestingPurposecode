 
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
below line is working fine when i am running it directing 
 SELECT TOP (100) 
            [Timestamp], 
            Mill_Id, 
            [Parameter], 
            SUM(Act_value) AS TotalActValue
        FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output]
        WHERE Mill_Id = 'Ext-coating' AND [Parameter] = 'API_COATING.TRF-3_AIRBLOW_HEATER_PREHEATING_UNIT_EXTRUDER12_SHOTBLAST_UTILITY_EXTRNALCOAT'
        GROUP BY [Timestamp], Mill_Id, [Parameter]
        ORDER BY [Timestamp] DESC;

but when I am calling this procedure using below code it is not working 
        public DataTable getChartdata(string Mill,string Feeder)
        {
            //string mill = "Mill1";
           
            SqlCommand cmd = new SqlCommand("Proc_GetMonthlyEms_Model_data", Pimscn);
            cmd.CommandType = CommandType.StoredProcedure;
            cmd.Parameters.Add("@mill",SqlDbType.VarChar).Value= Mill;
            cmd.Parameters.Add("@feeder", SqlDbType.VarChar).Value = Feeder;
            SqlDataAdapter da = new SqlDataAdapter(cmd);
            DataTable dt = new DataTable();
            da.Fill(dt);

            return dt;
        }
        see i am passing this exec Proc_GetMonthlyEms_Model_data 'Ext-coating','API_COATING.TRF-3_AIRBLOW_HEATER_PREHEATING_UNIT_EXTRUDER12_SHOTBLAST_UTILITY_EXTRNALCOAT'
