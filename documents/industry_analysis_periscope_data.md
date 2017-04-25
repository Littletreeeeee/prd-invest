---
layout: default
---
[返回文档目录](../)

### Periscope Data Raises $9.5M Led By DFJ To Build Its “Tableau For Data Scientists”

 Periscope Data is a startup that has built a platform for data scientists to create fast but highly detailed and customised visualisations — think Tableau, but for engineers — and it has raised $9.5 million in funding.

 In a business where success is often predicated on the perfect mix of useful and great product, money and the right connections, it’s a sign of how Periscope Data is on track in nailing all three.

 Periscope Data works by taking raw data from various sources — the most popular Glaser says are MySQL, PostgreSQL, Amazon Redshift, Salesforce and Microsoft SQL Server — and with a few short commands, lets engineers create graphics to help make better sense of the numbers.

 The key also is in how fast the service works: 150 times faster than any other visualisation tool on the market, the company claims. The speed is partly due to how Periscope Data was originally architected: the original startup idea, Glaser tells me, was to build a site where engineers could enter any kind of data query based on data in the cloud and get a fast answer (a kind of “Google for data engineers,” as it were). The visualisation tool was an accidental side project to this, but they found was being used much, much more than the rest.

 “We got 2,000 users for the first product, which was very fast and flexible and nothing like that that existed,” Glaser said. “We built the original version of Periscope Data for ourselves to look at the data of how the original service was being used. It was humbling when people turned out to be much more interested in that side project than they were in the main one.”

 Periscope Data has up to now largely grown by word of mouth and very little marketing, and even so it’s picked up steam in an impressive way.

 Interestingly, while so much technology today feels like it’s out there to “replace” less efficient human labor, Periscope Data sees itself as serving a different purpose.

“So often companies like ours focused on data science, the pitch they give to companies is that there will never be enough data analysis in the world, but if you buy our software you won’t need to hire as many data scientists,” said Glaser. “But the best companies are hiring whole teams of these analysts to compete. That’s something you will continue to have to do, and you should be giving them the best tools to do their jobs.”

It’s also part of a bigger wave of startups coming out that are also trying to do the same thing, some of which are also, interestingly focusing on visualising all that abstract data. CartoDB, for example, focuses on mapping specifically.

### What’s in a Name? Leading Periscope Data’s $25M Series B.

Investors love pattern recognition. When something works, we look for patterns that inform future successful investments as well.

The company is laser-focused on creating software that empowers SQL data analysts to do their job better. Periscope gives analysts an “Iron Man suit” (to quote Harry, their CEO) that lets them run queries lightning fast and share dynamic dashboards with business stakeholders.

This cycle of query-dashboard-share is the main workflow for analysts in data-driven organizations, but is a cumbersome, manual process today, and there has been little software innovation to date to make it more efficient.

SQL analysts are very much a type of developer, and their numbers will continue to grow as data becomes increasingly critical to organizations across all sectors and stages. These analysts have traditionally been overlooked by software vendors, who have focused on selling BI software top down through business teams, which then ask analysts to use tools that they dislike and that are not designed for their needs. Periscope Data, in contrast, builds expressly for the analyst, and their go-to-market involves selling to individual analysts and small data teams, in much the same way that many of the developer platforms in our portfolio target individual developers.

### Building analytics at 500px

On the infrastructure side, I needed two things:
* A database that combines the logs and MySQL that is easy to query
* A front end to this database. Something that is easy to use so that anyone can query the data model with minimal help
With this, I could remove myself from being a dependency to all data analysis and pulls around the company.

Fortunately, we don’t have to reinvent the wheel. There exists products which solve exactly these two needs. In the next two sections I’m going to talk about implementing the Redshift database and Periscope.

**New Infrastructure, Part 1 — Redshift:**

#### Dimensional data warehouse
Kimball introduced in the 90's the idea of the dimensional data warehouse. This is a SQL database specialized for business analysis, different than production databases for applications. It is meant to be easy to understand and query.

Dimensional data warehouses have a special schema, with two types of tables called fact tables and dimension tables. Data would flow from the source (MySQL and logs), to these two types of tables in a process called Extract-Transform-Load (ETL).

The **schemas of data warehouses** have two types of tables:
* Fact tables record measurements in time of a process.
* Dimension tables record information about a particular item.

Both tables are meant to be understandable. They must have readable column names and values.

If we wanted to know how many likes in the past week, we could query the likes fact table, sum the records, and filter by date.

But if we wanted a breakdown of likes by user subscription type, querying the fact table would not be enough. subscription_type is not contained in the likes fact table. But user_id is. We would need to join the likes fact table to the users dimension table by user_id, and then group by user subscription_type.

From this example, its easy to understand why fact tables often contain foreign keys to dimension tables. This is so that they can be joined so that facts can be sliced by dimensions.

#### Extract-Transform-Load(ETL)
In order to populate our data warehouse with data from our production databases and logs, we apply a process called ETL.

Data warehouses look very different than production databases and log files. Tables are often denormalized, and complexity is hidden. For example, in production to see if a user has an active subscription, we might need to write a complex query involving several tables. But in a data warehouse, it should simply be a yes/no column in the users table.

By hiding a lot of the complexity to querying the data in the ETL, accessing data becomes much easier and fewer mistakes are made.

This data model contains both log and MySQL data — in a single place where no extra work needs to be done to combine them. It is simple to understand and read. All the complex queries such as determining the account type of a user that I used to do are now hidden in the ETL. Querying this data model is a pleasure.

But sometimes our data model falls short:
1. If we don’t have the right table set up for our question
2. If we need fancy processing that goes beyond SQL

With proper planning and iterating on our solution, we minimize 1). SQL is powerful. Questions in the form of 2) are complex, and are often beyond the scope of a business intelligence platform.

#### Amazon Redshift
Now that we talked a bit about the schema for our data warehouse, we need an actual database to host it.
There are technical requirements for databases that can support a dimensional model.

The main operations on such a database are aggregations and joins. These two operations are expensive on traditional databases, intended to be the data store for applications. Try joining a 100m table to a 500m table in MySQL. Even Postgres would buckle. We needed something specialized.

We needed something specialized for analytics use. Luckily for us there exists a cheap, easy to use, easy to maintain database that is optimized for analytical queries such as joins and aggregations: Amazon Redshift on AWS.

A Redshift database with 2tb of disk space, sufficient for our needs, costs a little over $4000 a year.

#### Building the ETL
ETL pipelines get very messy very quickly. You will find that your production database isn’t all that well thought out. Getting certain kinds of information needed for the ETL will require hacks and lots of dependencies.

Production schemas are awful for analytics.An ETL script that has to turn messy production data into clean data warehouse data will naturally be extremely messy.

I chose the Luigi framework to build the ETL pipelines on for many reasons:
* Luigi is built and supported by Spotify. It is used by thousands of start-ups around the world. There is an active mailing list and a growing community.
* Luigi is open source and extendable. Its clear what is going on, and extending the base classes are encouraged.
Luigi is Python based, a popular language for handling data.
* The code for Luigi is very readable. The basic unit of Luigi are task classes that model an atomic ETL operation. They have three parts. A requirements part that includes pointers to other tasks that need to run before this task, the data transformation step, and the output. Each task is thus a node in a directed graph — the ETL job. Luigi will actually visualize this graph for you in its visualizer, and give you diagnostic information about each run of the ETL.
* Structuring the code this way makes things easier to read and maintain. There’s both hierarchy and relationships. I also put all the tasks that feed into a final table on Redshift into one file. This allows me to quickly see the structure of the code, and isolate the pieces that I’m interested in.
* Luigi is idempotent. Meaning running it twice one after the other won’t do anything. It will only re-run parts of the code again if you remove the output of those particular tasks. This is supremely helpful in debugging. If a run fails at a particular node, you simply have to fix that node, before hitting run again. You don’t have to isolate that piece of code, and painstakingly run it in isolation.
* Luigi has error logging and status emails. You get a notification if a node fails during a run. You can then view the error stack trace, fix the code and rerun the ETL.

#### Periscope
With the data warehouse built, there was now a need for a tool that could be used around the company to access it.

* The simplest and cheapest solution would have been to give everyone a SQL terminal. But its not easy or friendly to use, and there was still friction to learning SQL.

* Looker was really enticing, as its interface allowed for non technical users to access the database and build dashboards and reports. But paying 30k+ a year was outside of our budget.

* Tableau was a powerful tool for visual analysis, but its falls short outside this range. It can’t do reporting and list pulls very easily. It also needs to store data in memory before computing, rather than relying on Redshift’s excellent data processing abilities.

None of these solutions fit. I found my answer in Periscope, an early stage start-up that built a product which was exactly what I was looking for.

A bit about Periscope:
* Periscope is a lightweight analytics tool where you can write SQL to create charts.
* Periscope can connect to Redshift easily as they are both cloud solutions.
* The dashboard is the basic document type.
* On these dashboards users can write SQL to create charts and tables.
* It is collaborative. Unlimited users can sign up within a company and see each other’s dashboards. Links can be sent to other users in Slack and email to point them towards certain dashboards.
* Each dashboard updates hourly automatically so you get current data.
* You can set each dashboard to send itself out as an email at set times.

This tool is killer:
* It is SQL based so learning SQL is a requirement. But by making dashboards so easy to share, using Periscope became a social activity inside the company. This created a strong incentive for people to learn SQL.
* Ad hoc analysis in Periscope is fast. Because its so quick to create charts, you can iterate quickly on how you are trying to look at the data and what questions you are asking. I feel that iteration is the most natural mode of work for an analyst. By making iterations fast, you get to the answer you are looking for much faster.
* Reporting in Periscope is easy. To pull a list, simply create a new dashboard, write your SQL, and hit save. You can then share a link to the dashboard or export the table as a CSV.
* Periscope is a full featured dashboard tool. One of the first things I did after I set up Periscope is to build dashboards for each product and team that we have. I then set these dashboards to email everyone each morning.
Periscope is collaborative. Everyone in the company can join and see what everyone else is working on. Users can add charts to each others dashboards, or edit the SQL behind each other’s existing charts. Users that are stuck with a query can quickly and easily ask for help.
* The cost was reasonable. For a company of over 60 people, I was happy at the monthly price that we were charged.

After setting up Periscope, a new channel of communication opened up inside the company — a data communication layer.

### Announcing Periscope Data’s $9.5M Series A

Bafflingly, professional analysts, who spend all day driving the hardest, most complex, most sophisticated business decisions, have been left out in the cold — until now.




## Periscope Data 概览
“Turn SQL into beautiful interactive dashboards to share with your entire company.”

### SQL编辑器
支持查询修订历史记录，自动补全，自动规范格式和通用语法助手。
支持存储SQL模板。 将经常使用的代码存储下来。

### 数据可视化
支持自定义可交互图表。支持拖拽选择维度和指标。支持自定义Dashboard上图表的大小和位置。
无需编辑SQL，支持自定义过滤、drilldowns、以及数据透视表。

### 分享
支持通过连接分享图表和Dashboard。支持设置邮件发送图表和报告。支持将图表嵌入网页。

### 数据库同步
支持多数据库的关联整合。数据库实时同步。保证稳定性。

### 快速查询结果
通过分布式架构，提升查询速度。

### 权限管理
简单快捷的数据和用户权限管理。

## Periscope Data 使用方式
### 1. 连接Periscope Data
支持的数据库包括:
* SQL Database: PostgreSQL, MySQL, SQL Server, Oracle Database, MemSQL
* Data Warehouses: Amazon Redshift, Vertica, Google BigQuery, Snowflake
* Analytics Databases: Segment SQL, Amplitude
* Other integration: Salsesforce

### 2. 开始使用
输入Query -> 选择图表展示类型 -> 保存

快捷键：
#### Dashboard Shortcuts
/ - Search dashboards
F - Search charts
C or N - Create new Chart
Esc - Close header menu

#### Chart Editing Pane
⌘+S or Ctrl+S - Save chart
Escape - Exit to dashboard
⌘+Enter or Ctrl+Enter - Run query
Shift+Tab - Backdent line
⌘+Shift+L or Ctrl+Shift+L - Auto-format SQL
⌘+/ or Ctrl+/ - Toggle line comments

### 3. 图表和Dashboard
Schema layout: 提供可查询的表格和columns的列表。
选择图表类型：Table, Line, Bar, Number Overlay, Pie, Area, Cohort Grid, Map, Sparkline, Scatter, Bubble, Image。
可编辑图表名称、描述

### 4. 数据表格
### 5. 可视化图表
### 6. 地图数据
### 7. 其他图表及设置
### 8. 管理
### 9. SQL格式器
### 10. 基础过滤
### 11. 高级过滤
### 12. 更多SQL工具
### 13. 分享和嵌入
### 14. 数据管理
### 15. SSO(Single Sign On)接入


## 参考资料

[The Second Coming of IT](https://www.theinformation.com/the-second-coming-of-it)
[Periscope Data Raises $9.5M Led By DFJ To Build Its “Tableau For Data Scientists”](https://techcrunch.com/2015/10/22/periscope-data/)
[What’s in a Name? Leading Periscope Data’s $25M Series B.](https://medium.com/think-with-bvp/whats-in-a-name-leading-periscope-data-s-25m-series-b-8c8c509ec104)
[Building analytics at 500px](https://medium.com/@samson_hu/building-analytics-at-500px-92e9a7005c83)
[Announcing Periscope Data’s $9.5M Series A](https://medium.com/@periscopedata/announcing-periscope-data-s-9-5m-series-a-f5ad101db078)
