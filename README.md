					  IF SUBSTR(CGP_CD_COAT, 0, 1) = 'Z' THEN
 
					          LN_Z_COAT_AMT := SUBSTR(CGP_CD_COAT, 2, 5);
 
					          IF LN_Z_COAT_AMT <> 0 THEN
 
						            LN_AZ_COAT := NVL(LN_Z_COAT_AMT, 1) / 7.14 / 1000;
 
					          END IF;
 
				          END IF;
 
				          IF SUBSTR(CGP_CD_COAT, 0, 1) = 'A' THEN
 
					          LN_AZ_COAT_AMT := SUBSTR(CGP_CD_COAT, 3, 5);
 
					          IF LN_AZ_COAT_AMT <> 0 THEN
 
						            LN_AZ_COAT := NVL(LN_AZ_COAT_AMT, 1) / 3.78 / 1000;
 
					          END IF;
 
				          END IF;


  SELECT
                        CASE
                            WHEN NVL(CGP_CD_COAT,' ') LIKE 'Z%' AND REGEXP_LIKE(SUBSTR(CGP_CD_COAT,2,5),'^\d+$')
                                THEN TO_NUMBER(SUBSTR(CGP_CD_COAT,2,5)) / 7.14 / 1000
                            WHEN NVL(CGP_CD_COAT,' ') LIKE 'A%' AND REGEXP_LIKE(SUBSTR(CGP_CD_COAT,3,5),'^\d+$')
                                THEN TO_NUMBER(SUBSTR(CGP_CD_COAT,3,5)) / 3.78 / 1000
                            ELSE NULL
                        END AS LN_AZ_COAT
                    FROM T_GP2_PDI
                    WHERE CGP_ID_COIL = 'K5B0791000'
                    ORDER BY CGP_TOC DESC
