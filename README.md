SELECT
    NVL(l1_pass_number, 0) AS l1_pass_number
FROM
    t_periodic_value_log
WHERE
    date_time BETWEEN
        TO_TIMESTAMP('04-12-2025 09.16.18.679000000 PM','DD-MM-YYYY HH.MI.SS.FF AM')
    AND
        TO_TIMESTAMP('04-12-2025 09.53.18.679000000 PM','DD-MM-YYYY HH.MI.SS.FF AM');