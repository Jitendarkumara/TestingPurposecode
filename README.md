 
 select CGP_CD_COAT 
 
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
                          
                          from T_GP2_pdi
