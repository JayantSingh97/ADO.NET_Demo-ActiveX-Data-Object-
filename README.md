# ADO.NET_Demo-ActiveX-Data-Object- Crud Tutorial
ActiveX Data Object tutorial


# working with stored procedure in Ado.net

        public  object GetDataFromDBbyStoredProcedures(string SQLConnection,string ProcedureName)
        {
            var _dataTable = new DataTable();
            _dataTable.Columns.Add("YourColumn", typeof(string));

            using (var connection = new SqlConnection(SQLConnection))
            {
                var cmd = new SqlCommand(ProcedureName, connection)
                {
                    CommandType = CommandType.StoredProcedure,
                    CommandTimeout = 0
                };
                connection.Open();
                var dr = cmd.ExecuteReader();
                if(dr.HasRows)
                    while (dr.Read())
                    {
                        var _dataRow = _dataTable.NewRow();
                        _dataRow[0] = dr[0];
                    }
            }

            return _dataTable;
        }
        
  # working with stored procedure with parameters in Ado.net
  
        public object GetDataFrom_DB_by_StoredProceduresWith_Parameters(string SQLConnection, string ProcedureName)
        {
            var _dataTable = new DataTable();
            _dataTable.Columns.Add("YourColumn", typeof(string));

            using (var connection = new SqlConnection(SQLConnection))
            {
                var cmd = new SqlCommand(ProcedureName, connection)
                {
                    CommandType = CommandType.StoredProcedure,
                    CommandTimeout = 0
                };
                var param = new SqlParameter();
                param.ParameterName = "@param1";
                param.Value = "All";
                cmd.Parameters.Add(param);

                param = new SqlParameter();
                param.ParameterName = "@param2";
                param.Value = "All";
                cmd.Parameters.Add(param);

                connection.Open();
                var dr = cmd.ExecuteReader();
                if (dr.HasRows)
                    while (dr.Read())
                    {
                        var _dataRow = _dataTable.NewRow();
                        _dataRow[0] = dr[0];
                    }
            }

            return _dataTable;
        }
        
        
        
 # data reading in Ado.net using query string
 
        public object GetDataFrom_DB_by_StoredProceduresWith_querystrings(string SQLConnection, string querystring)
        {
            var _dataTable = new DataTable();
            _dataTable.Columns.Add("YourColumn", typeof(string));

            using (var connection = new SqlConnection(SQLConnection))
            {
                var cmd = new SqlCommand(querystring, connection)
                {
                    CommandType = CommandType.Text,
                    CommandTimeout = 0
                }; 
                connection.Open();
                var dr = cmd.ExecuteReader();
                if (dr.HasRows)
                    while (dr.Read())
                    {
                        var _dataRow = _dataTable.NewRow();
                        _dataRow[0] = dr[0];
                    }
            }

            return _dataTable;
        }
        
        
        
   # Bulk operation in db using Ado.net 
   
        public virtual void Bulk_operationInDb_using_Ado_dot_net(string SQLConnection, DataTable _dataTable)
        {
            using (var connection = new SqlConnection(SQLConnection))
            {
                var bulkCopy = new SqlBulkCopy(
                    connection,
                    SqlBulkCopyOptions.TableLock | SqlBulkCopyOptions.FireTriggers |
                    SqlBulkCopyOptions.UseInternalTransaction, null);

                bulkCopy.DestinationTableName = "SourceTabelinDb";

                connection.Open();
                // column mapping with Db Table
                var column = new SqlBulkCopyColumnMapping("column", "column");
                bulkCopy.ColumnMappings.Add(column);
                var column1 = new SqlBulkCopyColumnMapping("column1", "column1");
                bulkCopy.ColumnMappings.Add(column1);

                bulkCopy.WriteToServer(_dataTable);
                connection.Close();
                _dataTable.Clear();
            }
        }
        
        
   # Transaction in Ado.net for data integrity.
   
        public object Transaction_using_Ado_dot_net(string SQLConnection, string ProcedureName)
        {
            var _dataTable = new DataTable();
            _dataTable.Columns.Add("YourColumn", typeof(string));

            using (var connection = new SqlConnection(SQLConnection))
            {
                using (var _sqlTransaction = connection.BeginTransaction())
                {
                    try
                    {
                        var cmd = new SqlCommand(ProcedureName, connection)
                        {
                            CommandType = CommandType.StoredProcedure,
                            CommandTimeout = 0
                        };
                        var param = new SqlParameter();
                        param.ParameterName = "@param1";
                        param.Value = "All";
                        cmd.Parameters.Add(param);

                        param = new SqlParameter();
                        param.ParameterName = "@param2";
                        param.Value = "All";
                        cmd.Parameters.Add(param);

                        connection.Open();
                        var dr = cmd.ExecuteReader();
                        if (dr.HasRows)
                            while (dr.Read())
                            {
                                var _dataRow = _dataTable.NewRow();
                                _dataRow[0] = dr[0];
                            }

                        // on successful Transaction commit the changes in DB
                        _sqlTransaction.Commit();
                    }

                    catch (Exception EX_NAME)
                    {
                        // on error   rollback the changes in DB
                        _sqlTransaction.Rollback();
                    }
                    finally
                    {
                        connection.Close();
                    }
                }

                return _dataTable;
            }
        }
   
