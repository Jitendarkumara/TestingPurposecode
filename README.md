SELECT TCIP_PRODUCT_COIL, TCIP_CIL_END_TIME  
FROM T_COL_COIL_INFO_PDO  
WHERE DATE(TCIP_CIL_END_TIME) = CURDATE()  
ORDER BY TCIP_CIL_END_TIME DESC;