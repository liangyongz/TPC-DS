# TPC-DS

## 使用流程

1. 使用TPC-DS创建MySQL表，使用TPC-DS构造数据，导入数据到MySQL表中
2. 集成MySQL表到Spark
3. 执行querys目录下的100条SQL

## 目录说明

### DSGen-software-code-3.2.0rc1

> 在官网下载的TPC-DS_Tool(https://www.tpc.org/tpc_documents_current_versions/current_specifications5.asp)
>
> 下载完成后，到tools目录下执行`make`命令编译，执行完成后按下面步骤使用。



#### 1.MySQL创表

```shell
mysql -hmysql_host -umysql_user -pmysql_pwd  

#创建新库tpcds_01
create database tpcds_01 DEFAULT CHARSET utf8 COLLATE utf8_general_ci;

#创建tpcds相关表
mysql -hmysql_host -umysql_user -pmysql_pwd tpcds_01 < DSGen-software-code-3.2.0rc1/tools/tpcds.sql
```

创表完成。



#### 2.TPC-DS数据构造

使用`dsdgen`命令生成测试数据

```shell
mkdir /tmp/tpcds_data_01
cd DSGen-software-code-3.2.0rc1/tools
##构造1G数据
./dsdgen -DIR /tmp/tpcds_data_01 -SCALE 1 -TERMINATE N, tpcds.sql
```

数据构造完成。

注：参数说明

| **参数**   | **说明**                                                     | **示例**                                                     |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| -sc        | 测试数据量的大小。                                           | 10表示10GB，1000表示1000GB（1TB）                            |
| -dir       | 生成的数据文件写入的目录。                                   | data1tb                                                      |
| -TERMINATE | 每行最后是否加字段分隔符。                                   | N或者YN：每行最后不加字段分隔符。Y：每行最后添加字段分隔符。比如分隔符\|。 |
| -PARALLEL  | 一共分成几个chunk。一条语句只能生成一个chunk。因此设置了几个，就要执行几次。 | 5                                                            |
| -CHILD     | 当前命令生成第几个chunk。                                    | 1                                                            |

例如，要构建1TB的测试数据，5个chunk来运行

```shell
mkdir data1tb_5

./dsdgen -sc 1000 -dir data1tb_5 -TERMINATE N -PARALLEL 5 -CHILD 1
./dsdgen -sc 1000 -dir data1tb_5 -TERMINATE N -PARALLEL 5 -CHILD 2
./dsdgen -sc 1000 -dir data1tb_5 -TERMINATE N -PARALLEL 5 -CHILD 3
./dsdgen -sc 1000 -dir data1tb_5 -TERMINATE N -PARALLEL 5 -CHILD 4
./dsdgen -sc 1000 -dir data1tb_5 -TERMINATE N -PARALLEL 5 -CHILD 5
```



#### 3. 数据导入

执行SQL，将数据导入MySQL表中

```sql
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/call_center.dat' INTO TABLE tpcds_01.call_center FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/customer.dat' INTO TABLE tpcds_01.customer FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/income_band.dat' INTO TABLE tpcds_01.income_band FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/ship_mode.dat' INTO TABLE tpcds_01.ship_mode FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/warehouse.dat' INTO TABLE tpcds_01.warehouse FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/catalog_page.dat' INTO TABLE tpcds_01.catalog_page FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/customer_demographics.dat' INTO TABLE tpcds_01.customer_demographics FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/inventory.dat' INTO TABLE tpcds_01.inventory FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/store.dat' INTO TABLE tpcds_01.store FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/web_page.dat' INTO TABLE tpcds_01.web_page FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/catalog_returns.dat' INTO TABLE tpcds_01.catalog_returns FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/date_dim.dat' INTO TABLE tpcds_01.date_dim FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/item.dat' INTO TABLE tpcds_01.item FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/store_returns.dat' INTO TABLE tpcds_01.store_returns FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/web_returns.dat' INTO TABLE tpcds_01.web_returns FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/catalog_sales.dat' INTO TABLE tpcds_01.catalog_sales FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/dbgen_version.dat' INTO TABLE tpcds_01.dbgen_version FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/promotion.dat' INTO TABLE tpcds_01.promotion FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/store_sales.dat' INTO TABLE tpcds_01.store_sales FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/web_sales.dat' INTO TABLE tpcds_01.web_sales FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/customer_address.dat' INTO TABLE tpcds_01.customer_address FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/household_demographics.dat' INTO TABLE tpcds_01.household_demographics FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/reason.dat' INTO TABLE tpcds_01.reason FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/time_dim.dat' INTO TABLE tpcds_01.time_dim FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
LOAD DATA LOCAL INFILE '/tmp/tpcds_data_01/web_site.dat' INTO TABLE tpcds_01.web_site FIELDS TERMINATED BY '|' LINES TERMINATED BY '\n';
```

数据导入完成。

至此TPC-DS测试场景构建完成，后续执行querys目录下SQL即可。

## querys

SQL目录，共100条SQL



