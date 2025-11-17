SELECT
    CGP_CD_COAT,

    CASE 
        WHEN SUBSTR(CGP_CD_COAT, 1, 1) = 'Z' THEN
            CASE 
                WHEN TO_NUMBER(SUBSTR(CGP_CD_COAT, 2, 5)) <> 0 THEN
                    NVL(TO_NUMBER(SUBSTR(CGP_CD_COAT, 2, 5)), 1) / 7.14 / 1000
            END
        WHEN SUBSTR(CGP_CD_COAT, 1, 1) = 'A' THEN
            CASE
                WHEN TO_NUMBER(SUBSTR(CGP_CD_COAT, 3, 5)) <> 0 THEN
                    NVL(TO_NUMBER(SUBSTR(CGP_CD_COAT, 3, 5)), 1) / 3.78 / 1000
            END
    END AS LN_AZ_COAT

FROM T_GP2_PDI;