create or replace PROCEDURE P_LINE_TEN_CALC_DATA_MODIFIED (
    p_id_coil     VARCHAR2,
    p_thickness   NUMBER,
    p_width       NUMBER,
    p_tdc_no      VARCHAR2
)
AS
    h_stm_id VARCHAR2(50);

    l_uncoiler NUMBER := 0;
    l_ent_accu NUMBER := 0;
    l_process_fctr NUMBER := 0;
    l_hbr NUMBER := 0;
    l_ext_accu NUMBER := 0;

    l_rec_ten_nor_less NUMBER := 0;
    l_rec_ten_acrelic_less NUMBER := 0;
    l_rec_ten_nor_grt NUMBER := 0;
    l_rec_ten_acrelic_grt NUMBER := 0;

    l_por1_ent NUMBER := 0;
    l_por2_ent NUMBER := 0;
    l_spm_entry NUMBER := 0;
    l_spm_exit NUMBER := 0;
    l_tll_ten NUMBER := 0;

    -- Temperature sets
    l_nof_temp NUMBER := 0;
    l_rtf_temp NUMBER := 0;
    l_sf_temp  NUMBER := 0;
    l_snout_temp NUMBER := 0;
    l_bath_temp NUMBER := 0;
    l_strip_temp NUMBER := 0;

    l_type VARCHAR2(10);
BEGIN
    DBMS_OUTPUT.PUT_LINE(
        'PROC: THK=' || p_thickness || ' WIDTH=' || p_width ||
        ' TDC=' || p_tdc_no
    );

    ----------------------------------------------------------------------
    -- 1. GET TENSION FACTORS (SAFE)
    ----------------------------------------------------------------------
    h_stm_id := 'SELECT_STRESS_FCTR';

    BEGIN
        SELECT 
            UNCOILER * p_thickness * p_width,
            ENT_ACCU * p_thickness * p_width,
            PROCESS_FCTR * p_thickness * p_width,
            HBR * p_thickness * p_width,
            EXT_ACCU * p_thickness * p_width,
            REC_TEN_NOR_PASS_LESS_10MT * p_width * p_thickness,
            REC_TEN_ACRELIC_LESS_10MT * p_width * p_thickness,
            REC_TEN_NOR_PASS_GRT_10MT * p_width * p_thickness,
            REC_TEN_ACRELIC_GRT_10MT * p_width * p_thickness,
            POR1_ENT * p_width * p_thickness,
            POR2_ENT * p_width * p_thickness,
            SPM_ENTRY * p_width * p_thickness,
            SPM_EXIT * p_width * p_thickness,
            TLL_TEN
        INTO
            l_uncoiler, l_ent_accu, l_process_fctr, l_hbr, l_ext_accu,
            l_rec_ten_nor_less, l_rec_ten_acrelic_less,
            l_rec_ten_nor_grt, l_rec_ten_acrelic_grt,
            l_por1_ent, l_por2_ent, l_spm_entry, l_spm_exit, l_tll_ten
        FROM T_GP2_STRESS_FCTR
        WHERE p_thickness BETWEEN THK_FROM AND THK_TO;

    EXCEPTION WHEN NO_DATA_FOUND THEN
        DBMS_OUTPUT.PUT_LINE('NO STRESS_FCTR FOUND: THK='||p_thickness);
        RETURN;
    END;


    ----------------------------------------------------------------------
    -- 2. GET TDC TYPE
    ----------------------------------------------------------------------
    h_stm_id := 'SELECT_TDC_TYPE';

    BEGIN
        SELECT TDC_TYPE INTO l_type
        FROM GP02KL2.T_GP2_TDC_MASTER
        WHERE TDC_NUMBER = p_tdc_no;
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            DBMS_OUTPUT.PUT_LINE('NO TDC MASTER FOUND FOR: '||p_tdc_no);
            RETURN;
    END;


    ----------------------------------------------------------------------
    -- 3. TEMPERATURE READINGS DEPENDING ON TYPE
    ----------------------------------------------------------------------
    IF l_type = 'SOFT' THEN
        h_stm_id := 'SOFT_TEMP';

        BEGIN
            SELECT 
                RTF_EXIT_TEMP, SF_EXIT_TEMP,
                SNOUT_TEMP, BATH_TEMP, STRIP_TEMP--, NOF_EXIT_TEMP
            INTO 
                l_rtf_temp, l_sf_temp,
                l_snout_temp, l_bath_temp, l_strip_temp
               -- , l_nof_temp
            FROM T_GL_SOFT_CYCLE
            WHERE p_thickness BETWEEN THK_FROM AND THK_TO
              AND TDC_NUMBER = p_tdc_no;

        EXCEPTION WHEN NO_DATA_FOUND THEN
            DBMS_OUTPUT.PUT_LINE('NO SOFT CYCLE DATA FOUND');
            RETURN;
        END;

    ELSE 
    
        h_stm_id := 'HARD_TEMP';

        BEGIN
            SELECT 
                RTF_EXIT_TEMP, SF_EXIT_TEMP,
                SNOUT_TEMP, BATH_TEMP, STRIP_TEMP, NOF_EXIT_TEMP
            INTO 
                l_rtf_temp, l_sf_temp,
                l_snout_temp, l_bath_temp, l_strip_temp, l_nof_temp
            FROM T_GL_HARD_CYCLE
            WHERE p_thickness BETWEEN THK_FROM AND THK_TO;

        EXCEPTION WHEN NO_DATA_FOUND THEN
            DBMS_OUTPUT.PUT_LINE('NO HARD CYCLE DATA FOUND');
            RETURN;
        END;
    END IF;


    ----------------------------------------------------------------------
    -- 4. INSERT FINAL VALUES
    ----------------------------------------------------------------------
    h_stm_id := 'INSERT_DATA';

    INSERT INTO T_GP2_TENSION_VALUE_COPY (
        CGP_ID_COIL, WIDTH, THICKNESS,
        UNCOILER, POR1_ENT, POR2_ENT, ENT_ACCU,
        PROCESS_TEN, HBR, EXT_ACCU,
        REC_TEN_NOR_PASS_LESS_10MT, REC_TEN_ACRELIC_LESS_10MT,
        REC_TEN_NOR_PASS_GRT_10MT, REC_TEN_ACRELIC_GRT_10MT,
        SPM_ENTRY, SPM_EXIT, TLL_TEN,
        NOF_EXIT_TEMP, RTF_EXIT_TEMP, SF_EXIT_TEMP,
        SNOUT_TEMP, BATH_TEMP
    )
--    VALUES (
--        'K630910000', 904, 0.239,
--        216.056, 216.056, 216.056,
--        324.084, 324.084, 432.112, 503.41048,
--        540.14, 864.224,
--        648.168, 972.252,
--        216.056, 216.056, 1296.336,
--        0, 1583.69048, 3000,
--        200, 300
--    );
    VALUES (
        p_id_coil, p_width, p_thickness,
        l_uncoiler, l_por1_ent, l_por2_ent,
        l_ent_accu, l_process_fctr, l_hbr, l_ext_accu,
        l_rec_ten_nor_less, l_rec_ten_acrelic_less,
        l_rec_ten_nor_grt, l_rec_ten_acrelic_grt,
        l_spm_entry, l_spm_exit, l_tll_ten,
        l_nof_temp, l_rtf_temp, l_sf_temp,
        l_snout_temp, l_bath_temp
    );
commit;
EXCEPTION
    WHEN OTHERS THEN
        HANDL_EERROR(
            'GP2 L2',
            'P_LINE_TEN_CALC_DATA_MODIFIED',
            h_stm_id,
            SQLCODE,
            SUBSTR(SQLERRM,1,200),
            NULL,
            'T_GP2_TENSION_VALUE_COPY'
        );
END;
