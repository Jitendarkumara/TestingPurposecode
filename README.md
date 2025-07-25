        public (WriteValueCollection,StatusCodeCollection) WriteNodes(ISession session,string[] idPlcs,object[] values)
        {
            StatusCodeCollection results = null;
            //Write the configured nodes
            WriteValueCollection nodesToWrite = new WriteValueCollection();

            if (session == null || session.Connected == false)
            {
                Trace.WriteLine("Session not connected!");
                return (nodesToWrite,results);
            }

            try
            {
               
                // Int32 Node - Objects\CTT\Scalar\Scalar_Static\Int32
                for (int i = 0; i < idPlcs.Count(); i++)
                {
                    try
                    {
                        var rv = session.ReadValue(new NodeId(idPlcs[i]));
                        WriteValue intWriteVal = new WriteValue();
                        intWriteVal.NodeId = new NodeId(idPlcs[i]);
                        intWriteVal.AttributeId = Attributes.Value;
                        intWriteVal.Value = new DataValue();
                        intWriteVal.Value.Value = ChangeType(values[i], rv.WrappedValue.TypeInfo);// Convert.ChangeType(values[i],TypeCode.Double .GetType(rv.WrappedValue.TypeInfo.ToString()));
                        nodesToWrite.Add(intWriteVal);
                    }
                    catch (Exception EX1)
                    {

                       Trace.WriteLine($"Writing Error idplc : {idPlcs[i]} value : {values[i]} | {EX1.Message}");
                    }
                    
                }

                // Write the node attributes
                
                DiagnosticInfoCollection diagnosticInfos;
                // Call Write Service
                session.Write(null,
                                nodesToWrite,
                                out results,
                                out diagnosticInfos);

                // Validate the response
                m_validateResponse(results, nodesToWrite);


                foreach (StatusCode writeResult in results)
                {
                    Trace.WriteLine($" writeResult : {writeResult}");
                    
                }
            }
            catch (Exception ex)
            {
                // Log Error
                Trace.WriteLine($" Write Nodes Error : {ex.Message}.");
            }
            return (nodesToWrite, results);
        }
Opc.Ua.ServiceResultException
  HResult=0x80131500
  Message=BadSessionIdInvalid
  Source=Opc.Ua.Core
  StackTrace:
   at Opc.Ua.ClientBase.ValidateResponse(ResponseHeader header)
   at Opc.Ua.SessionClient.Read(RequestHeader requestHeader, Double maxAge, TimestampsToReturn timestampsToReturn, ReadValueIdCollection nodesToRead, DataValueCollection& results, DiagnosticInfoCollection& diagnosticInfos)
   at Opc.Ua.SessionClientBatched.Read(RequestHeader requestHeader, Double maxAge, TimestampsToReturn timestampsToReturn, ReadValueIdCollection nodesToRead, DataValueCollection& results, DiagnosticInfoCollection& diagnosticInfos)
   at Opc.Ua.Client.Session.ReadValue(NodeId nodeId)
   at Opc.Ua.Client.TraceableSession.ReadValue(NodeId nodeId)
   at OPCUAClientLib.ClientUtils.WriteNodes(ISession session, String[] idPlcs, Object[] values) in C:\Users\Administrator\Desktop\GP1_Application\TataOPCUANet\TataOPCUANet\OPCUAClientNetLib\ClientUtil.cs:line 247

  This exception was originally thrown at this call stack:
    [External Code]
    OPCUAClientLib.ClientUtils.WriteNodes(Opc.Ua.Client.ISession, string[], object[]) in ClientUtil.cs
