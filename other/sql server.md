create table #Data(name varchar(100),row varchar(100),reserved varchar(100),data varchar(100),index_size varchar(100),unused varchar(100)) 
 
declare @name varchar(100) 
declare cur cursor  for 
    select name from sysobjects where xtype='u' order by name 
open cur 
fetch next from cur into @name 
while @@fetch_status=0 
begin 
    insert into #data 
    exec sp_spaceused   @name 
    print @name 
 
    fetch next from cur into @name 
end 
close cur 
deallocate cur 
 
create table #DataNew(name varchar(100),row int,reserved int,data int,index_size int,unused int) 
 
insert into #dataNew 
select name,convert(int,row) as row,convert(int,replace(reserved,'KB','')) as reserved,convert(int,replace(data,'KB','')) as data, 
convert(int,replace(index_size,'KB','')) as index_size,convert(int,replace(unused,'KB','')) as unused from #data  
 
select * from #dataNew order by data desc    



--主要原理： 
exec sp_spaceused '表名' --取得表占用空間 
exec sp_spaceused ''--數據庫所有空間 

还有一个简单的办法

SELECT   a.name, b.rows
FROM      sysobjects AS a INNER JOIN
                 sysindexes AS b ON a.id = b.id
WHERE   (a.type = 'u') AND (b.indid IN (0, 1))
ORDER BY b.rows DESC
