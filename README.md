  SUB_COIL := SUBSTR(MOTHER_COIL, 1, LENGTH(MOTHER_COIL) - ZERO_NUMBER);

  IF SHEAR_MIN < 1 THEN
    SHEAR_MIN := 0;
  ELSE
    SELECT COUNT(TCIP_INPUT_COIL)
    INTO PART_NUM
    FROM T_COL_COIL_INFO_PDO
    WHERE TCIP_INPUT_COIL = MOTHER_COIL;

    PART_NUM := PART_NUM + 1;
    DBMS_OUTPUT.PUT_LINE('PART_NUM: ' || PART_NUM);


    -- Logic to generate the daughter coil ID
    IF MOTHER_COIL LIKE '%0' THEN
      -- Existing logic for mother coil ending with zero
      CASE ZERO_NUMBER
        WHEN 1 THEN
          DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM);
        WHEN 2 THEN
          DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM * 10);
        WHEN 3 THEN
          DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM * 100);
        WHEN 4 THEN
          DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM * 1000);
      END CASE;
    ELSE
      -- New logic for mother coil not ending with zero
      DAUGHTER_COIL := SUB_COIL || TO_CHAR(PART_NUM + 1); -- Increment by 1 for mother coils not ending with zero
   
    END IF;
