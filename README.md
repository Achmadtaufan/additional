
DECLARE @Username NVARCHAR(100) = 'nama_user'; -- Ganti 'nama_user' dengan nama user yang ingin kamu cek aksesnya

DECLARE @DatabaseName NVARCHAR(100);
DECLARE @Access NVARCHAR(10);

DECLARE db_cursor CURSOR FOR
SELECT name
FROM sys.databases
WHERE state_desc = 'ONLINE';

OPEN db_cursor;
FETCH NEXT FROM db_cursor INTO @DatabaseName;

WHILE @@FETCH_STATUS = 0
BEGIN
    SET @Access = '';
    IF HAS_DBACCESS(@DatabaseName) = 1
        SET @Access = 'Granted';

    PRINT 'Database: ' + @DatabaseName + ', Access: ' + @Access;

    FETCH NEXT FROM db_cursor INTO @DatabaseName;
END

CLOSE db_cursor;
DEALLOCATE db_cursor;
