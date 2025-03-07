ALTER PROCEDURE [dbo].[Proc_GetMonthlyEms_Model_data]
@mill varchar(20),
@feeder varchar(20)
AS
BEGIN
if(@feeder='All')
	SELECT TOP (100) Timestamp,Mill_Id,sum(Act_value) as TotalActvalue
 FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output] where Mill_Id=@mill group by [Timestamp],Mill_Id  order by [Timestamp] desc
 else
 SELECT TOP (100) Timestamp,Mill_Id,parameter ,sum(Act_value) as TotalActvalue
 FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output] where Mill_Id=@mill and Parameter=@feeder group by [Timestamp],Mill_Id,Parameter  order by [Timestamp] desc

END
