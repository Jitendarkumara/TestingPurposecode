using System;
using System.Diagnostics;

class Program
{
    static void Main()
    {
        try
        {
            int x = 5 / int.Parse("0"); // This will cause an exception
        }
        catch (Exception ex)
        {
            StackTrace st = new StackTrace(ex, true);
            Console.WriteLine("Error at Line: " + st.GetFrame(0).GetFileLineNumber());
        }
    }
}