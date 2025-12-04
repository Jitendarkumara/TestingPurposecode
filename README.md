 SELECT
           nvl(l1_pass_number,0) as l1_pass_number
        --INTO p_out_thk
        FROM
            t_periodic_value_log
        WHERE
            date_time BETWEEN '04-12-2025 09.16.18.679000000 PM' AND '04-12-2025 09.53.18.679000000 PM'
