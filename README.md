# additional


DECLARE @Username NVARCHAR(100) = 'nama_user'; -- Ganti 'nama_user' dengan nama user yang ingin kamu cek aksesnya

DECLARE @Query NVARCHAR(MAX) = '';

SELECT @Query += 
    'SELECT ''' + name + ''' AS DatabaseName, 
            CASE WHEN HAS_DBACCESS(''' + name + ''') = 1 THEN ''Granted'' ELSE '''' END AS Access
    FROM ' + name + '; '
FROM sys.databases;

EXEC sp_executesql @Query;
