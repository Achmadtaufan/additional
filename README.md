# Additional
SELECT 
    j.name AS 'JobName',
    s.step_id AS 'StepID',
    s.step_name AS 'StepName',
    h.step_name AS 'LastExecutedStepName',
    h.run_date AS 'LastRunDate',
    h.run_time AS 'LastRunTime',
    h.message AS 'LastRunMessage'
FROM msdb.dbo.sysjobs j
JOIN msdb.dbo.sysjobsteps s ON j.job_id = s.job_id
LEFT JOIN (
    SELECT 
        j.job_id,
        j.name AS 'JobName',
        h.step_id,
        s.step_name,
        h.run_date,
        h.run_time,
        h.message,
        ROW_NUMBER() OVER(PARTITION BY j.job_id ORDER BY h.run_date DESC, h.run_time DESC) AS 'RowNum'
    FROM msdb.dbo.sysjobs j
    JOIN msdb.dbo.sysjobhistory h ON j.job_id = h.job_id
    JOIN msdb.dbo.sysjobsteps s ON h.step_id = s.step_id AND h.job_id = s.job_id
    WHERE h.run_status = 0 -- Failed
) AS h ON j.job_id = h.job_id AND h.RowNum = 1
WHERE j.enabled = 1 -- Only enabled jobs
