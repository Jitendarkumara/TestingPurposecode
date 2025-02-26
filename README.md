	SELECT TOP (100) Timestamp,Mill_Id,sum(Act_value) as TotalActvalue
 FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output] where Mill_Id=@mill group by [Timestamp],Mill_Id  order by [Timestamp] desc
