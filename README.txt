对Relaen-cli（主要用于生成数据表对应实体）部分代码的学习

（一）basegenerator:定义了一个名为 BaseGenerator 的抽象类，它是一个基础生成器，用于根据不同的数据库方言生成实体代码。

（1）类成员变量
   config：数据库配置，从 config.json 中获取。
   tables：表映射，存储实体名和表名的对应关系。
   relations：外键关联数组，存储数据库中的外键关系。
   typeMap：数据库类型与 JavaScript/TypeScript 类型的关系映射。
（2）构造函数
   接收一个 IConfig 类型的配置对象，并初始化 tables 和 typeMap。
（3）抽象方法
   getConn()：获取数据库连接。
   closeConn(conn: any)：关闭数据库连接。
   genTables(conn: any)：生成表实体。
   getFields(conn: any, tableName: string)：获取字段数组。
   genRelations(conn: any)：生成外键关系。
（4）生成实体代码的方法
   gen()：生成所有实体代码。这个方法执行以下步骤：
   1.读取配置文件并解析为 JSON 对象。
   2.根据配置文件中的 dialect 字段，创建相应的数据库生成器实例。
   3.获取数据库连接。
   4.生成实体和表映射。
   5.生成外键关系。
   6.遍历 tables 映射，为每个实体生成代码，并写入文件。
（5）生成实体代码的辅助方法
   genEntity(conn: any, entityName: string)：为给定的实体生成代码。
   handleRelation(relArr: IRelation[])：处理外键关系，将其转换为标准格式。
   genName(name: string, sp: string, st: number, stUpcase: number)：生成实体名或字段属性名。
   getEntityByTbl(tblName: string)：通过表名获取实体名。
   getRelation(entity: string, column: string)：获取字段对应关系。
   getRefered(entity: string, column: string)：获取被引用的外键关系。
   updType(column: IColumn)：更新字段类型，将其转换为 TypeScript 类型。
   getRefRel(column: string, relArr: IColumn[])：查询字段是否既是外键也是主键。

（二）MysqlGenerator：定义了一个名为 MysqlGenerator 的类，它继承自 BaseGenerator 类，并实现了针对 MySQL 数据库的具体逻辑。以下是代码的详细解释：

（1）类成员变量
   config：数据库配置，从 config.json 中获取。
   tables：表映射，存储实体名和表名的对应关系。
   relations：外键关联数组，存储数据库中的外键关系。
   typeMap：数据库类型与 JavaScript/TypeScript 类型的关系映射。
（2）方法实现
   1.getConn()：
     使用 mysql 模块创建并返回一个 MySQL 数据库连接对象。
   2.genTables(conn)：
     切换到指定的数据库。
     查询 INFORMATION_SCHEMA.TABLES 表，获取所有表名。
     为每个表生成实体名，并存储到 tables 映射中。
   3.closeConn(conn)：
     关闭数据库连接。
   4.getFields(conn, tableName)：
     查询指定表的字段信息。
     为每个字段生成 IColumn 对象，并返回字段数组。
   5.genRelations(conn)：
     切换到 information_schema 数据库。
     查询 key_column_usage 表，获取外键信息。
     查询 referential_constraints 表，获取外键的删除和更新规则。
     为每个外键生成 IRelation 对象，并存储到 relations 数组中。
     调用 handleRelation 方法处理外键关系。
    6.changeDb(conn, db)：
     切换数据库连接到指定的数据库。