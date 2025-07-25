public (WriteValueCollection, StatusCodeCollection) WriteNodes(ISession session, string[] idPlcs, object[] values)
{
    StatusCodeCollection results = null;
    WriteValueCollection nodesToWrite = new WriteValueCollection();

    if (session == null || !session.Connected)
    {
        Trace.WriteLine("WriteNodes Error: OPC UA session is not connected or is null.");
        return (nodesToWrite, results);
    }

    try
    {
        for (int i = 0; i < idPlcs.Length; i++)
        {
            try
            {
                NodeId nodeId = new NodeId(idPlcs[i]);

                WriteValue writeValue = new WriteValue
                {
                    NodeId = nodeId,
                    AttributeId = Attributes.Value,
                    Value = new DataValue
                    {
                        Value = values[i]
                    }
                };

                nodesToWrite.Add(writeValue);
            }
            catch (Exception exInner)
            {
                Trace.WriteLine($"[WriteNodes] Skipping node {idPlcs[i]} due to error: {exInner.Message}");
            }
        }

        if (nodesToWrite.Count == 0)
        {
            Trace.WriteLine("[WriteNodes] No valid nodes to write.");
            return (nodesToWrite, results);
        }

        // Call Write Service
        session.Write(null, nodesToWrite, out results, out DiagnosticInfoCollection diagnosticInfos);

        m_validateResponse(results, nodesToWrite);

        for (int i = 0; i < results.Count; i++)
        {
            Trace.WriteLine($"[WriteNodes] Node: {idPlcs[i]} - Write Result: {results[i]}");
        }
    }
    catch (ServiceResultException srex)
    {
        Trace.WriteLine($"[WriteNodes] ServiceResultException: {srex.Message}");
    }
    catch (Exception ex)
    {
        Trace.WriteLine($"[WriteNodes] General Exception: {ex.Message}");
    }

    return (nodesToWrite, results);
}