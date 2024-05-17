USE mis_test
GO

-- Buat table variabel Table_Used
DECLARE @Table_Used TABLE (
    table_name NVARCHAR(100)
)

-- Insert manual data ke dalam table variabel
INSERT INTO @Table_Used (table_name)
VALUES 
    ('EmployeeData2')

-- Deklarasi variabel untuk menyimpan pernyataan SQL dinamis
DECLARE @sql NVARCHAR(MAX)

-- Inisialisasi variabel SQL dengan pernyataan awal
SET @sql = ''

-- Bangun pernyataan SQL dinamis untuk setiap tabel dalam table variabel
SELECT @sql = @sql + 
            'SELECT DISTINCT ''' + tu.table_name + ''' AS table_name, ''' + c.COLUMN_NAME + ''' AS column_name ' +
            'FROM ' + QUOTENAME(tu.table_name) + ' ' + 
            'WHERE NOT EXISTS (' +
                'SELECT 1 FROM ' + QUOTENAME(tu.table_name) + ' WHERE ' + QUOTENAME(c.COLUMN_NAME) + ' IS NOT NULL AND ' + QUOTENAME(c.COLUMN_NAME) + ' != '''' ' +
            ') ' +
            'UNION ALL '
FROM @Table_Used tu
JOIN INFORMATION_SCHEMA.COLUMNS c ON tu.table_name = c.TABLE_NAME
WHERE OBJECTPROPERTY(OBJECT_ID(tu.table_name), 'IsUserTable') = 1

-- Hapus UNION ALL yang terakhir
SET @sql = LEFT(@sql, LEN(@sql) - LEN('UNION ALL'))

-- Eksekusi pernyataan SQL dinamis
EXEC(@sql)
