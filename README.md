catch (Exception ex)
{
    StackTrace st = new StackTrace(ex, true);
    int lineNumber = 0;
    
    if (st.FrameCount > 0)
    {
        lineNumber = st.GetFrame(0).GetFileLineNumber(); // Get exact line number
    }

    LogErrorToFile(ex.Message, lineNumber);
}