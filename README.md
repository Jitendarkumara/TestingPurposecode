SELECT 
    t_col_cot_pdi_l3.tc_coil_number, 
    t_col_cot_pdi_l3.TC_WEIGHT,
    t_col_cot_pdi_l3.TC_ID_MESSAGE
FROM 
    t_col_cot_pdi_l3
WHERE 
    REGEXP_LIKE(t_col_cot_pdi_l3.tc_id_message, '^\d{4}-\d{2}-\d{2}-\d{2}\.\d{2}\.\d{2}\.\d{6}$')
    AND TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') BETWEEN 
        TO_TIMESTAMP('2025-01-24 00:00:00', 'YYYY-MM-DD HH24:MI:SS') 
        AND TO_TIMESTAMP('2025-01-24 23:59:59', 'YYYY-MM-DD HH24:MI:SS')
    AND t_col_cot_pdi_l3.tc_coil_number NOT IN ('D1000', 'D2000')
    AND (
        -- Shift A: 06:00 to 14:00
        (TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') BETWEEN 
            TO_TIMESTAMP('2025-01-24 06:00:00', 'YYYY-MM-DD HH24:MI:SS') 
            AND TO_TIMESTAMP('2025-01-24 13:59:59', 'YYYY-MM-DD HH24:MI:SS') 
            AND :shift = 'A')
        -- Shift B: 14:00 to 22:00
        OR (TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') BETWEEN 
            TO_TIMESTAMP('2025-01-24 14:00:00', 'YYYY-MM-DD HH24:MI:SS') 
            AND TO_TIMESTAMP('2025-01-24 21:59:59', 'YYYY-MM-DD HH24:MI:SS') 
            AND :shift = 'B')
        -- Shift C: 22:00 to 06:00 (next day)
        OR ((TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') >= 
                TO_TIMESTAMP('2025-01-24 22:00:00', 'YYYY-MM-DD HH24:MI:SS') 
            OR TO_TIMESTAMP(t_col_cot_pdi_l3.tc_id_message, 'YYYY-MM-DD-HH24.MI.SS.FF6') < 
                TO_TIMESTAMP('2025-01-24 06:00:00', 'YYYY-MM-DD HH24:MI:SS')) 
            AND :shift = 'C')
    );