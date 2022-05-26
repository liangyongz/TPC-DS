# TPC-DS
## 流程
1. TPC-DS创建MySQL表、TPC-DS构造数据、导入数据到MySQL表中
2. 集成MySQL表到Spark
3. 使用query100查询Spark表数据

## 目录说明
### DSGen-software-code-3.2.0rc1
这是在官网下载的TPC-DS_Tool(https://www.tpc.org/tpc_documents_current_versions/current_specifications5.asp)。使用目录下的脚本构造数据和创表语句
创表语句：DSGen-software-code-3.2.0rc1/tools/tpcds.sql
构造测试数据：
```
cd DSGen-software-code-3.2.0rc1/tools
#构造1G数据
./dsdgen -DIR /tmp/tpcds_data_01 -SCALE 1 -TERMINATE N, tpcds.sql
```

