SELECT * 
FROM (
    SELECT 
        Timestamp, 
        Mill_Id, 
        Parameter, 
        Act_value, 
        Running_Status, 
        Alert 
    FROM [PIMS_KHOPOLI].[dbo].[Ems_Model_output] 
    WHERE Parameter IN (
        'API1.Panelno-11_TRF-8_HF_WELDER1_2_SEAM_ANNEALER_2',
        'API1.Panelno-10_TRF-7_OFF_LINE_UT_HYDRO_END_FACER_END_CROPPING',
        'API2.TRF-1_HF_WELDER',
        'API2.TRF-4_OFFLINEUST_EDGEMILLER_FINISH_PDB_FINPASS_VARNISH',
        'API_COATING. TRF-2_INDUCTION_2'
    ) 
) AS SourceTable
PIVOT (
    MAX(Act_value) 
    FOR Parameter IN (
        [API1.Panelno-11_TRF-8_HF_WELDER1_2_SEAM_ANNEALER_2],
        [API1.Panelno-10_TRF-7_OFF_LINE_UT_HYDRO_END_FACER_END_CROPPING],
        [API2.TRF-1_HF_WELDER],
        [API2.TRF-4_OFFLINEUST_EDGEMILLER_FINISH_PDB_FINPASS_VARNISH],
        [API_COATING. TRF-2_INDUCTION_2]
    )
) AS PivotTable
ORDER BY Timestamp DESC;