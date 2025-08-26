CREATE VIEW my_view AS
SELECT *
FROM T_periodic_value_log t
WHERE t.DATE_TIME = (
    SELECT MAX(DATE_TIME) FROM T_periodic_value_log
);