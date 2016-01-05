#数据库DDL（Data Definition Language）
* 添加字段
```mysql
alter table jkxy_jiuye_analytics_students add (
pay_status tinyint(4) NOT NULL DEFAULT '0' COMMENT '付款状态：1：首尾款，3：全款',
current int(11) NOT NULL DEFAULT '0' COMMENT '当前任务序号：-1：正常完成任务，-2：D终止，-3：F终止',
progress tinyint(11) NOT NULL DEFAULT '0' COMMENT '进度状态：1：低于预期，2：符合预期，3：超预期',
job_id int(11) unsigned NOT NULL DEFAULT '0' COMMENT '班级所属职业方向想',
key seq_idx(seq)
);
```
