# SQL Server Installation
The following are the installation procedures for a standard install of SQL server on a new server through GUI. 
 
1.	Set server power plan to High.
2.	Create local “Documents” folder on C: drive and copy SQL-Setup-AuxilaryFiles there.
3.	Navigate to the local folder containing the RTM install and launch the Setup.exe
4.	Click the Installation option on the left pane then choose New SQL Server stand-alone installation or Add Features to an existing installation
5.	Cycle through each screen.  On the Feature Selection screen, select Database Engine Service and other features as needed.
6.	Unless applications specify a named instance, use Default instance on the Instance Configuration screen
7.	On The Server Configuration screen change the Account Name for SQL Server Agent and SQL Server Database Engine, SQL Server Analysis Services, & SQL Server Integration Services to the service account that was created for this instance.  
a.	Enter the password for each account. 
b.	Set Startup Type to Automatic for all services (except SQL Server Browser which should be disabled by default)
8.	On the Database Engine Configuration screen:
a.	Server Configuration Tab
i.	We will typically use Mixed Mode 
ii.	Enter the strong SA password that was generated in the Prerequisites tasks
iii.	Add DBA group in the SQL Server Administrators box
b.	Data Directories Tab
i.	Data Root Directory: J:\sqldata
ii.	User database log directory:  L:\sqllogs
iii.	Backup directory: Z:\sqlbackups
iv.	Temp DB directory:  T:\sqldata and T:\sqllogs
9.	Begin Install
10.	After install, ensure services are running and connection through SSMS can be made
a.	Disable the CEIP services
11.	Enable permanent trace flags by opening SQL Configuration Manager, right-click the "SQL Server" service an choose properties
a.	Click Startup Parameters and add the following entries
i.	-T1117 (autogrow equally)  DISCONTINUED for 2017
ii.	-T1118 (full extents only)  DISCONTINUED for 2017
iii.	-T1224 (disables lock escalation)
iv.	-T2371 (dynamic threshhold for stat update instead of 20% so triggers at lower level for large tables)
v.	-T4199 (enable query optimizer hotfixes)
b.	Click Apply then OK
c.	Restart SQL Service
d.	Using SSMS run DBCC TRACESTATUS to ensure flags have been turned on 
12.	Open Administrative tools | Local Security Policy
a.	Go to Local Policies then User Rights Assignment
i.	Open Perform volume maintenance tasks and add the SQL Server service account for this server (done during install with SQL 2017)
13.	Install latest SQL Service Pack and CU. (if available)
14.	Run SQL-Setup-DBAWORK\DBAWORK_Setup.sql
a.	If needed detach and move .mdf and .log files to correct folders
15.	SQL Server Properties (use the following defaults unless otherwise specified)
a.	Set  Maximum Server Memory - leave 10% for OS
b.	Set Max Degree of Parallelism as needed
16.	Configure Tempdb - Determine number of files and calculate maximum size for data files and log
•	Total tempdb size should be approximately 10% of the total size of databases
•	If your server has less than 8 logical cores (e.g. a one CPU server with 4 physical cores and hyperthreading enabled has 8 logical cores), use # tempdb data files = # logical cores, equally sized
•	If your server has more than 8 logical cores, start with 8 tempdb data files
•	Data and log file to be presized to use most of disk and auto growth disabled.
17.	Install prerequisite scripts
a.	OlaMaintenanceSolution.sql  -this creates backup, index defrag, and db integrity check jobs
i.	https://ola.hallengren.com/
ii.	Schedule maintenance jobs - Run OlaJobSchedules.sql to set initial times
b.	Who_Is_Active.sql  -this creates the sp_WhoIsActive proc in the master database
i.	http://whoisactive.com/
18.	Run Post-Installation scripts located in SQL-Setup-PerformanceMonitor
a.	IndexAllFragmentation.sql - performance monitoring
19.	Once Production Databases have been setup run SQL-Setup-Alerts: Generate SQL_alerts.sql. Change the output to "Results to Text" and copy and run the results in a new query window. You may need to change the text output on your machine to max characters to get the full script.
20.	If SQL 2017 or higher Run: Tasks > Vulnerability Assessment on each database and review/remediate findings
 

