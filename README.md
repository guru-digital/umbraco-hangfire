
# Deprecated
[![deprecated](http://badges.github.io/stability-badges/dist/deprecated.svg)](http://github.com/badges/stability-badges)
[![No Maintenance Intended](http://unmaintained.tech/badge.svg)](http://unmaintained.tech/)

This repository will no longer be maintained. I have created a private repository for use by Guru Digital Media to integrate with Umbraco9.

# Hangfire scheduled tasks for Umbraco 8#
[![CI](https://github.com/guru-digital/umbraco-hangfire/actions/workflows/NetFxCI.yml/badge.svg)](https://github.com/guru-digital/umbraco-hangfire/actions/workflows/NetFxCI.yml)


This integrates https://github.com/HangfireIO/Hangfire with Umbraco v8 https://github.com/umbraco/Umbraco-CMS

## Installation ##
Refer to Umbraco documentation to set up Umbraco development environment https://our.umbraco.com/download
>### NuGet ###
```
PM> Install-Package UmbracoHangfire
```
>### Manually ###
* Add UmbracoHangfire project to your solution
* Add project reference to UmbracoHangfire
* Copy the contents of UmbracoHangfire\App_Plugins to the corresponding folder in your Umbraco project

## Umbraco Integration ##

* A Hangfire Tree node is added under Settings
 * Jobs are listed
 * Change Cron settings for any job
* The Hangfire Dashboard can be accessed by admin users at [yoururl]/umbraco/hangfire

## Example Usage ##

```csharp
[RuntimeLevel(MinLevel = RuntimeLevel.Boot)]
public class DemoJob : IComposer
{
	public const string DemoJobName = "DemoJob";

	[HangfireJob("Demo Job")]
	public static void Execute()
	{
		// Add code to perform action here
		new HangfireDbContext().SaveHistory(DemoJobName, "Demo Job Completed Successfully", DateTime.Now);
	}

	/// <summary>
	/// Create job in startup of website
	/// </summary>
	public static void CreateRecurringJob()
	{
		// Create recurring job
		RecurringJobManager manager = new RecurringJobManager();
		RecurringJobDto job = HangfireJobForm.JobFromId(DemoJobName);
		if (job == null)
		{
			RecurringJob.AddOrUpdate(DemoJobName, () => DemoJob.Execute(), "0 0 * * *", TimeZoneInfo.Local);
		}
	}

	public void Compose(Composition composition)
	{
		HangfireStartup.HangFireStarted += HangfireStartup_HangFireStarted;
	}

	private void HangfireStartup_HangFireStarted(object sender, HangfireStartedArgs e)
	{
		CreateRecurringJob();
	}
}
```
