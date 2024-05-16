# additional

DECLARE @ProcedureName NVARCHAR(128) = 'anjing';
DECLARE @SQL NVARCHAR(MAX);

SET @SQL = 
'SELECT 
    QUOTENAME(s.name) + ''.'' + QUOTENAME(p.name) AS ProcedureName,
    DB_NAME() AS DatabaseName
FROM 
    [DB_NAME()].sys.procedures p
INNER JOIN 
    [DB_NAME()].sys.schemas s ON p.schema_id = s.schema_id
WHERE 
    p.name = @ProcedureName';

DECLARE @DBName NVARCHAR(MAX);
DECLARE cur CURSOR FOR 
    SELECT name 
    FROM sys.databases 
    WHERE state_desc = 'ONLINE' 
    AND database_id > 4; -- Exclude system databases

OPEN cur;
FETCH NEXT FROM cur INTO @DBName;

WHILE @@FETCH_STATUS = 0
BEGIN
    SET @SQL = REPLACE(@SQL, '[DB_NAME()]', QUOTENAME(@DBName));
    EXEC sp_executesql @SQL, N'@ProcedureName NVARCHAR(128)', @ProcedureName;
    FETCH NEXT FROM cur INTO @DBName;
END

CLOSE cur;
DEALLOCATE cur;
