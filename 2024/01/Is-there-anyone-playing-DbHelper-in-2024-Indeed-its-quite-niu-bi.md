---
title: 2024年了还有人玩DbHelper？- 确实很6
slug: Is-there-anyone-playing-DbHelper-in-2024-Indeed-its-quite-niu-bi
description: 2024年了，DbHelper永不过时，看看大佬写的DbHelper，你一定佩服！
date: 2024-01-21 00:13:47
lastmod: 2024-01-21 00:27:47
banner: true
copyright: Reprinted
author: 游子吟i
originalTitle: 基于Ado.Net多个关系型数据库DbHelper封装
originalLink: https://blog.csdn.net/ftfmatlab/article/details/135655836
draft: false
cover: https://img1.dotnet9.com/2024/01/cover_09.png
categories: .NET
tags: 技术更新
---

## 引言

时光荏苒，转眼已是 2024 年。在这个技术日新月异的时代，有些经典却永远不会过时。`DbHelper`，这个在开发者社区中一直备受瞩目的名字，如今依然熠熠生辉。最近，一位技术大佬再次展示了 DbHelper 的强大魅力，让我们一起来欣赏一下吧！

![](https://img1.dotnet9.com/2024/01/0901.png)

在阅读了这篇`深入浅出`的文章后，站长我深受启发，忍不住要为大家推荐这位大佬的佳作。无论你是初学者还是资深开发者，相信都能从中收获满满。

- 原文链接：https://blog.csdn.net/ftfmatlab/article/details/135655836
- 源码地址：https://download.csdn.net/download/ftfmatlab/88765289

## DbHelper 的封装：简约而不简单

基于[ADO.NET](https://learn.microsoft.com/zh-cn/dotnet/framework/data/adonet/)框架，这位大佬巧妙地封装了适用于多个关系型数据库的`DbHelper`。通过简洁明了的代码，实现了对各种数据库的高效操作。

```csharp
public class DbHelper
    {
        private readonly DataBase _dataBase;
        public DbHelper(DataBase dataBase)
        {
            _dataBase = dataBase;
        }
        public DataBase GetDataBase()
        {
            return _dataBase;
        }
        public DbConnection GetDbConnection()
        {
            var conn = _dataBase.CreationConnection();

            if (conn.State == ConnectionState.Closed)
            {
                conn.Open();
            }

            return conn;
        }
        /// <summary>
        /// 执行语句
        /// </summary>
        /// <param name="sql">sql语句</param>
        /// <param name="cmdParms">参数</param>
        /// <returns></returns>
        public int Execute(string sql, params DbParameter[] cmdParms)
        {
            using (DbConnection connection = GetDbConnection())
            {
                using (DbCommand cmd = connection.CreateCommand())
                {
                    try
                    {
                        PrepareCommand(cmd, connection, null, sql, cmdParms);
                        int rows = cmd.ExecuteNonQuery();
                        cmd.Parameters.Clear();
                        return rows;
                    }
                    catch (DbException e)
                    {
                        throw e;
                    }
                }
            }
        }
        /// <summary>
        /// 批量查询
        /// </summary>
        /// <param name="sql">sql语句</param>
        /// <param name="cmdParms">参数</param>
        /// <returns></returns>
        public DataSet Query(string sql, params DbParameter[] cmdParms)
        {
            using (DbConnection connection = GetDbConnection())
            {
                DataSet ds = new DataSet();
                try
                {
                    DbProviderFactory factory = DbProviderFactories.GetFactory(connection);
                    DbCommand command = factory.CreateCommand();
                    PrepareCommand(command, connection, null, sql, cmdParms);
                    DbDataAdapter adapter = factory.CreateDataAdapter();
                    adapter.SelectCommand = command;
                    adapter.Fill(ds, "ds");
                    adapter.Dispose();
                    command.Dispose();
                }
                catch (DbException ex)
                {
                    throw ex;
                }
                return ds;
            }
        }
        /// <summary>
        /// 批量查询
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="sql">sql语句</param>
        /// <param name="reader">数据读取器</param>
        /// <param name="cmdParms">参数</param>
        /// <returns></returns>
        /// <exception cref="Exception"></exception>
        public List<T> Query<T>(string sql, Func<IDataReader, T> reader, params DbParameter[] cmdParms)
        {
            if (reader == null)
                throw new Exception("数据读取器是空的!");

            List<T> list = new List<T>();
            using (DbConnection connection = GetDbConnection())
            {
                using (DbCommand cmd = connection.CreateCommand())
                {
                    try
                    {
                        PrepareCommand(cmd, connection, null, sql, cmdParms);
                        DbDataReader myReader = cmd.ExecuteReader();
                        cmd.Parameters.Clear();
                        while (myReader.Read())
                        {
                            list.Add(reader(myReader));
                        }
                        myReader.Close();
                    }
                    catch (DbException e)
                    {
                        throw e;
                    }
                }
            }

            return list;
        }
        /// <summary>
        /// 单个查询
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="sql">sql语句</param>
        /// <param name="reader">数据读取器</param>
        /// <param name="cmdParms">参数</param>
        /// <returns></returns>
        /// <exception cref="Exception"></exception>
        public T QueryFirstOrDefault<T>(string sql, Func<IDataReader, T> reader, params DbParameter[] cmdParms)
        {
            if (reader == null)
            {
                throw new Exception("数据读取器是空的!");
            }

            var model = default(T);
            using (DbConnection connection = GetDbConnection())
            {
                using (DbCommand cmd = connection.CreateCommand())
                {
                    try
                    {
                        PrepareCommand(cmd, connection, null, sql, cmdParms);
                        DbDataReader myReader = cmd.ExecuteReader();
                        cmd.Parameters.Clear();
                        if (myReader.Read())
                            model = reader(myReader);

                        myReader.Close();
                    }
                    catch (DbException e)
                    {
                        throw e;
                    }
                }
            }

            return model;
        }
        /// <summary>
        /// 执行存储过程
        /// </summary>
        /// <param name="storedProcName">存储过程名</param>
        /// <param name="parameters">存储过程参数</param>
        /// <returns></returns>
        public DataSet RunProcedure(string storedProcName, DbParameter[] parameters)
        {
            using (DbConnection connection = GetDbConnection())
            {
                DataSet dataSet = new DataSet();
                connection.Open();
                DbDataAdapter sqlDA = DbProviderFactories.GetFactory(connection).CreateDataAdapter();
                sqlDA.SelectCommand = BuildQueryCommand(connection, storedProcName, parameters);
                sqlDA.Fill(dataSet, "ds");
                sqlDA.SelectCommand.Dispose();
                sqlDA.Dispose();
                return dataSet;
            }
        }
        /// <summary>
        /// 执行存储过程，返回SqlDataReader ( 注意：调用该方法后，一定要对SqlDataReader进行Close )
        /// </summary>
        /// <param name="storedProcName">存储过程名</param>
        /// <param name="parameters">存储过程参数</param>
        /// <returns>SqlDataReader</returns>
        public DbDataReader RunProcedureToReader(string storedProcName, DbParameter[] parameters)
        {
            using (DbConnection connection = GetDbConnection())
            {
                DbDataReader returnReader;
                connection.Open();
                DbCommand command = BuildQueryCommand(connection, storedProcName, parameters);
                command.CommandType = CommandType.StoredProcedure;
                returnReader = command.ExecuteReader(CommandBehavior.CloseConnection);
                command.Dispose();
                return returnReader;
            }
        }
        /// <summary>
        /// 执行存储过程
        /// </summary>
        /// <param name="storedProcName">存储过程名</param>
        /// <param name="parameters">存储过程参数</param>
        /// <returns>SqlDataReader</returns>
        public T RunProcedure<T>(string storedProcName, Func<IDataReader, T> reader, DbParameter[] parameters)
        {
            if (reader == null)
            {
                throw new Exception("数据读取器是空的!");
            }

            T t = default(T);
            using (DbConnection connection = GetDbConnection())
            {
                DbDataReader returnReader;
                connection.Open();
                DbCommand command = BuildQueryCommand(connection, storedProcName, parameters);
                command.CommandType = CommandType.StoredProcedure;
                returnReader = command.ExecuteReader(CommandBehavior.CloseConnection);
                command.Dispose();
                if (returnReader.Read())
                    t = reader(returnReader);
                returnReader.Close();
            }
            return t;
        }
        /// <summary>
        /// 执行存储过程
        /// </summary>
        /// <param name="storedProcName">存储过程名</param>
        /// <param name="parameters">存储过程参数</param>
        /// <returns>SqlDataReader</returns>
        public List<T> RunProcedureToList<T>(string storedProcName, Func<IDataReader, T> reader, DbParameter[] parameters)
        {
            if (reader == null)
            {
                throw new Exception("数据读取器是空的!");
            }

            List<T> list = new List<T>();
            using (DbConnection connection = GetDbConnection())
            {
                DbDataReader returnReader;
                connection.Open();
                DbCommand command = BuildQueryCommand(connection, storedProcName, parameters);
                command.CommandType = CommandType.StoredProcedure;
                returnReader = command.ExecuteReader(CommandBehavior.CloseConnection);
                command.Dispose();
                while (returnReader.Read())
                    list.Add(reader(returnReader));
                returnReader.Close();
            }
            return list;
        }
        /// <summary>
        /// 返回首行首列
        /// </summary>
        /// <param name="sql">sql语句</param>
        /// <param name="cmdParms">参数</param>
        /// <returns></returns>
        public object ExecuteScalar(string sql, params DbParameter[] cmdParms)
        {
            object result = null;
            using (DbConnection connection = GetDbConnection())
            {
                using (DbCommand cmd = connection.CreateCommand())
                {
                    try
                    {
                        PrepareCommand(cmd, connection, null, sql, cmdParms);
                        result = cmd.ExecuteScalar();
                    }
                    catch (DbException e)
                    {
                        throw e;
                    }
                }
            }
            return result;
        }
        /// <summary>
        /// 分页列表
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="tablename">表名(可以自定)</param>
        /// <param name="page">分页信息</param>
        /// <param name="reader">读取器</param>
        /// <param name="where">条件</param>
        /// <param name="field">字段</param>
        /// <param name="order">排序</param>
        public List<T> QueryWithPage<T>(string tablename, PageInfo page, Func<IDataReader, T> reader, string where = "", string field = "*", string order = "", params DbParameter[] cmdParms)
        {
            long offset = page.Index * page.PageSize;
            string sql = "SELECT " + field + " FROM " + tablename;
            sql = ListPageSql(sql, where, order);
            sql = sql + " " + Limit(offset, page.PageSize);
            string sql2 = "SELECT COUNT(0) FROM " + tablename;
            sql2 = ListPageSql(sql2, where, "");
            string sql3 = sql + ";" + sql2;
            List<T> list = new List<T>();
            using (DbConnection conn = GetDbConnection())
            {
                using (DbCommand cmd = conn.CreateCommand())
                {
                    try
                    {
                        PrepareCommand(cmd, conn, null, sql3, cmdParms);
                        DbDataReader myReader = cmd.ExecuteReader();
                        cmd.Parameters.Clear();
                        while (myReader.Read())
                        {
                            list.Add(reader(myReader));
                        }

                        if (myReader.NextResult() && myReader.Read())
                            page.Count = myReader.GetInt64Ex(0);

                        myReader.Close();
                    }
                    catch (MySqlException e)
                    {
                        throw new Exception(e.Message);
                    }
                }
            }

            return list;
        }
        /// <summary>
        /// 组装分页sql
        /// </summary>
        /// <param name="sql">基础sql</param>
        /// <param name="where">条件</param>
        /// <param name="order">排序</param>
        /// <returns></returns>
        private string ListPageSql(string sql, string where, string order)
        {
            if (!string.IsNullOrEmpty(where))
            {
                sql = sql + " WHERE " + where;
            }

            if (!string.IsNullOrEmpty(order))
            {
                sql = sql + " " + order;
            }

            return sql;
        }
        /// <summary>
        /// 分页
        /// </summary>
        /// <param name="offset">偏移</param>
        /// <param name="size">每页显示数据尺寸</param>
        /// <returns></returns>
        /// <exception cref="Exception"></exception>
        public string Limit(long offset, long size)
        {
            if (offset == -1)
            {
                if (_dataBase.DbType != DbBaseType.SqlServer)
                {
                    return "LIMIT " + size;
                }
            }
            else
            {
                if (_dataBase.DbType == DbBaseType.MySql)
                {
                    return string.Format("LIMIT {0},{1}", offset, size);
                }

                if (_dataBase.DbType == DbBaseType.PostgreSql || _dataBase.DbType == DbBaseType.Sqlite)
                {
                    return string.Format(" LIMIT {0} OFFSET {1}", size, offset);
                }
            }

            throw new Exception("暂时不支持其它分页语法");
        }
        public DbParameter CreateDbParameter(string parameterName, DbType dbType, object value)
        {
            using(DbConnection connection = GetDbConnection())
            {
                DbParameter dbParameter = DbProviderFactories.GetFactory(connection).CreateParameter();
                dbParameter.ParameterName = parameterName;
                dbParameter.DbType = dbType;
                dbParameter.Value = value;
                return dbParameter;
            }
        }
        protected void PrepareCommand(DbCommand cmd, DbConnection conn, DbTransaction trans, string cmdText, DbParameter[] cmdParms)
        {
            cmd.Connection = conn;
            cmd.CommandText = cmdText;
            if (trans != null)
                cmd.Transaction = trans;
            cmd.CommandType = CommandType.Text;
            SetParameters(cmd, cmdParms);
        }
        private DbCommand BuildQueryCommand(DbConnection connection, string storedProcName, DbParameter[] parameters)
        {
            DbCommand command = connection.CreateCommand();
            command.CommandText = storedProcName;
            command.CommandType = CommandType.StoredProcedure;
            SetParameters(command, parameters);
            return command;
        }
        private void SetParameters(DbCommand command, DbParameter[] cmdParms)
        {
            if (cmdParms != null)
            {
                foreach (var parameter in cmdParms)
                {
                    if (
                        (parameter.Direction == ParameterDirection.InputOutput
                        ||
                        parameter.Direction == ParameterDirection.Input)
                        &&
                        (parameter.Value == null))
                    {
                        parameter.Value = DBNull.Value;
                    }

                    command.Parameters.Add(parameter);
                }
            }
        }
    }
```

## Demo 项目中一探究竟

想要一睹 DbHelper 在实战中的风采？没问题，大佬已经为我们准备好了 Demo 项目。通过以下截图，我们可以先睹为快：

![](https://img1.dotnet9.com/2024/01/0902.png)

在 Demo 项目中，DataAchieve 类继承了 DataBase 抽象类，并重写了 CreationConnection()方法。通过 DataBaseFactory 工厂类，我们可以轻松创建 DataBase 实例。这种设计模式不仅提高了代码的复用性，还使得项目结构更加清晰。

![](https://img1.dotnet9.com/2024/01/0903.png)

该项目已上传，感兴趣的朋友可以下载下来一探究竟。

## 2024/01/20 新增多个参数生成

为了满足更多场景下的需求，大佬在近期对 DbHelper 进行了升级。新增的多个参数生成功能无疑将为开发者们带来更多便利。让我们一起来看看这个新特性吧！

![](https://img1.dotnet9.com/2024/01/0904.png)

大佬写作过于随性，大家可下载代码研究：

- 原文链接：https://blog.csdn.net/ftfmatlab/article/details/135655836
- 源码地址：https://download.csdn.net/download/ftfmatlab/88765289
