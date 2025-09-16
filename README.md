using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Logging.EventLog;
using OPCUAClientService;

   
        IHost host = Host.CreateDefaultBuilder(args)
            .ConfigureLogging(option =>
            {
                if (OperatingSystem.IsWindows())
                {
                    option.AddFilter<EventLogLoggerProvider>(level => level >= LogLevel.Trace);
                }
            })
            .ConfigureServices(services =>
            {
                services.AddHostedService<OPCUAWorker>();
                services.AddHostedService<MessageBackgroundService>();
               
            })
            .ConfigureWebHostDefaults(webBuilder =>
            {

                webBuilder.ConfigureLogging((hostingContext, logging) =>
                {
                    if (!hostingContext.HostingEnvironment.IsProduction())
                    {
                        logging.ClearProviders(); // Clear any existing logger providers
                        logging.AddFilter(level => level >= LogLevel.None);  // Add the NullLoggerProvider
                    }
                    else
                    {
                        logging.AddFilter(level => level >= LogLevel.Information);
                    }
                });
                webBuilder.UseStartup<Startup>();
            })
            .UseWindowsService()
            .Build();

        await host.RunAsync();
    
