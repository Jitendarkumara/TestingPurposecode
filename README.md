SELECT job_name, enabled, state, last_start_date, next_run_date
FROM dba_scheduler_jobs
WHERE job_name = 'YOUR_JOB_NAME';