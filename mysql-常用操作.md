## 获取服务器连接数量
```sql
SELECT SUBSTRING_INDEX(host, ':', 1) AS ip
	, COUNT(*) AS count
FROM information_schema.processlist
GROUP BY ip
ORDER BY count DESC;
```
