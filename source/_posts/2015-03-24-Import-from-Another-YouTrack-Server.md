title: "Import-from-Another-YouTrack-Server"
date: 2015-03-24 11:21:27
tags:
- youtrack
---

被 youtrack 虐过几次，对它有一点不怀好感。。。其实主要了解了它的数据库以后也就好多了，安装的时候一定要先把数据库位置安排好了，便于日后维护。如果忘记数据库在哪里了，那也可以在这个地方找到：c:\ProgramData\JetBrains\YouTrack\conf\internal\wizard-configured.properties 
（参见：http://confluence.jetbrains.com/display/YTD6/Changing+Database+Location）

今天是因为要转移 youtrack 了，所以在网上查了一下怎么把一台机器上的 youtrack 数据导入到另一台上。（参见 [Import from Another YouTrack Server - YouTrack 6.x Documentation - Confluence](https://confluence.jetbrains.com/display/YTD6/Import+from+Another+YouTrack+Server)）

To import all issues in a project from one YouTrack server to another:
1. Download the latest version of [YouTrack's Python Client Library](https://github.com/JetBrains/youtrack-rest-python-library/) and unzip it.
2. Ensure that either [Python](https://www.python.org/downloads/) is installed in your system.
3. Execute the following command:
   
```
python youtrack2youtrack.py source_url source_login source_password target_url target_login target_password projectId
```
    
The following command-line parameters should be provided:
    
|Parameter       |Description                                                             |
|----------------|------------------------------------------------------------------------|
|source_url      |An URL to a source YouTrack server you want to import issues from.      |
|source_login    |Username to log in to a source YouTrack server.                         |
|source_password |Password to log in to the source YouTrack server.                       |
|target_url      |An URL to a target YouTrack server you want to import issues to.        |
|target_login    |Username to log into the target YouTrack server.                        |
|target_password |Password to log in to the target YouTrack server.                       |
|projectId       |projectID of a project you want to import to the target YouTrack server.|