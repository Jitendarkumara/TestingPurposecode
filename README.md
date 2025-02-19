using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using Microsoft.Extensions.Configuration;

public class MachineSectionDAL
{
    private readonly string _connectionString;

    public MachineSectionDAL(IConfiguration configuration)
    {
        _connectionString = configuration.GetConnectionString("DefaultConnection");
    }

    public List<MachineSection> GetMachineSections()
    {
        var sections = new List<MachineSection>();

        using (SqlConnection conn = new SqlConnection(_connectionString))
        {
            conn.Open();
            using (SqlCommand cmd = new SqlCommand("GetMachineSections", conn)) // Stored Procedure
            {
                cmd.CommandType = CommandType.StoredProcedure;
                using (SqlDataReader reader = cmd.ExecuteReader())
                {
                    while (reader.Read())
                    {
                        var section = new MachineSection
                        {
                            Id = Convert.ToInt32(reader["Id"]),
                            SectionName = reader["SectionName"].ToString(),
                            Machines = new List<string>(), // Populate this separately
                            Parameters = new List<MachineParameter>() // Populate separately
                        };
                        sections.Add(section);
                    }
                }
            }

            // Fetch Machines for each section
            foreach (var section in sections)
            {
                section.Machines = GetMachinesBySectionId(conn, section.Id);
                section.Parameters = GetParametersBySectionId(conn, section.Id);
            }
        }
        return sections;
    }

    private List<string> GetMachinesBySectionId(SqlConnection conn, int sectionId)
    {
        var machines = new List<string>();
        using (SqlCommand cmd = new SqlCommand("GetMachinesBySectionId", conn))
        {
            cmd.CommandType = CommandType.StoredProcedure;
            cmd.Parameters.AddWithValue("@SectionId", sectionId);
            using (SqlDataReader reader = cmd.ExecuteReader())
            {
                while (reader.Read())
                {
                    machines.Add(reader["MachineName"].ToString());
                }
            }
        }
        return machines;
    }

    private List<MachineParameter> GetParametersBySectionId(SqlConnection conn, int sectionId)
    {
        var parameters = new List<MachineParameter>();
        using (SqlCommand cmd = new SqlCommand("GetParametersBySectionId", conn))
        {
            cmd.CommandType = CommandType.StoredProcedure;
            cmd.Parameters.AddWithValue("@SectionId", sectionId);
            using (SqlDataReader reader = cmd.ExecuteReader())
            {
                while (reader.Read())
                {
                    var parameter = new MachineParameter
                    {
                        Id = Convert.ToInt32(reader["Id"]),
                        ParameterName = reader["ParameterName"].ToString(),
                        Values = new List<double>() // Populate separately
                    };
                    parameters.Add(parameter);
                }
            }
        }

        // Fetch values for each parameter
        foreach (var param in parameters)
        {
            param.Values = GetParameterValues(conn, param.Id);
        }

        return parameters;
    }

    private List<double> GetParameterValues(SqlConnection conn, int parameterId)
    {
        var values = new List<double>();
        using (SqlCommand cmd = new SqlCommand("GetParameterValues", conn))
        {
            cmd.CommandType = CommandType.StoredProcedure;
            cmd.Parameters.AddWithValue("@ParameterId", parameterId);
            using (SqlDataReader reader = cmd.ExecuteReader())
            {
                while (reader.Read())
                {
                    values.Add(Convert.ToDouble(reader["Value"]));
                }
            }
        }
        return values;
    }
}