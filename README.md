SELECT 

    PDI.TC_COIL_NUMBER AS Mother_COIL,


PDI.TC_WEIGHT as Pdi_WEIGHT,
 PDI.tc_id_message AS PDI_Time,

   count( PDO.TCIP_PRODUCT_COIL) AS Daughter_COIL_Count,
    sum( PDO.TCPI_ACTUAL_WT) AS Pdo_WEIGHT,
--    PDO.TCIP_CIL_END_TIME AS End_Time,
    pdo.l3_flag

FROM 
    T_COL_COT_PDI_L3 PDI
JOIN 
    T_COL_COIL_INFO_PDO PDO
ON 
    PDI.TC_COIL_NUMBER = PDO.TCIP_INPUT_COIL  
  group by    PDI.TC_COIL_NUMBER,  pdo.l3_flag, PDI.tc_id_message
ORDER BY  
   PDI.TC_ID_Message DESC,
    PDO.TCIP_PRODUCT_COIL
