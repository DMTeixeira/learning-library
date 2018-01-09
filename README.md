![Snap2](images/overivew/image1.png)


![apex-round-128.pdf](images/overivew/image2.jpeg)

 
# Oracle Application Express: Developing Database Web Applications


# Hands-On-Labs Guide


## Overview


This series of hands-on labs is designed to teach you how to build applications using Application Express. The primary vehicle for teaching is a single use case which is expanded on with each lab.

The primary use case is that your team tracks projects you are currently working on. Rather than using a spreadsheet or some commercial project tracking tool you have been tasked with building a Web application so that the team has a custom application that meets everyone's requirements. The DBA has created a script which creates various tables and populates them with the current data.

In this series of hands-on-labs, you also quickly build two simple applications. You use a spreadsheet to create the Budget App application that allows everyone to maintain the project cost and budget data. The Hardware application is used to make purchasing decisions of the hardware in different departments. You will then greatly improve these two applications and utilize a number of techniques such as Interactive Grids, and Interactive Reports.

Prerequisites
=============

The steps and screenshots in this hands-on-labs use Oracle Application Express 5.1.

To run these hands-on-labs, you need to:

-   Obtain an Oracle Application Express Workspace and a Workspace Administrator /Developer user account. Select the most appropriate option from those listed below to obtain a Workspace:

    -   [apex.oracle.com](http://apex.oracle.com/) - Request a workspace on Oracleâ€™s free 'development only' service for evaluating the technology.

    -   [Oracle Database Cloud Service](https://cloud.oracle.com/database) - Request service on the Oracle Database Cloud Service. Once provisioned you will be provided access to your cloud service which includes an Oracle Application Express Workspace.

    -   [Oracle Database 11g Express Edition](http://www.oracle.com/technetwork/products/express-edition/overview/index.html) - Download Oracle XE and install on your laptop or desktop machine and then [download](http://www.oracle.com/technetwork/developer-tools/apex/downloads/index.html) and install the latest Oracle Application Express release.

    -   [Oracle VM Virtual Box](http://www.oracle.com/technetwork/database/enterprise-edition/databaseappdev-vm-161299.html) - Download Oracle VM Virtual Box and then import the [Database Application Development Appliance](http://www.oracle.com/technetwork/database/enterprise-edition/databaseappdev-vm-161299.html) which includes Oracle Database 12c, Application Express and a number of labs pre-installed.

-   The **apex-course-labs.zip** file extracted into your working directory.

The apex-course-labs.zip file includes the scripts required by some of the steps in the hands-on labs. For each unit, an application export file is also included. This application export is basically the completed application for all the hands-on labs in the unit. If you are unable to perform the hands-on labs in any unit, you can simply import and install the application export file for that particular unit. For example, application export for unit 16 is demo projects app-export-unit 16.sql.


## IMPORTANT: 

- The Lab documentation is **best viewed** by using the Workshop's [GitHub Pages Website URL](https://oracle.github.io/learning-library/workshops/microservices-devops). Once you are viewing the Workshop's GitHub Pages website, you can see a list of Lab Guides at any time by clicking on the **Menu Icon**

    ![](images/WorkshopMenu.png)

- To log issues and view the Lab Guide source, go to the [github oracle](https://github.com/oracle/learning-library/issues/new) repository.

- Visit the [Workshop Interactive Labguide](https://launch.oracle.com/?microservices-devops) for a visual overview of the workshop content. 

## Hands-on Labs Details

## Overview : Hands-on Overview

**Documentation**: [hol_overview_v1.0.2.md](hol_overview_v1.0.2.md)

## Unit 1: Getting Started with Oracle Application Express

**Documentation**: [hol01_v1.0.2.md](hol01_v1.0.2.md)

### Objectives

- Log in to Oracle APEX environment
- Navigating through the Major Components of Oracle APEX
- Installing and Running a Packaged Application


## Unit 2: Using SQL Workshop

**Documentation**: [hol01_v1.0.2.md](hol01_v1.0.2.md)

### Objectives

- HOL 2-1: Loading the Tables and Data
- HOL 2-2: Creating a Lookup Table

## Unit 3: Creating a Database Application

**Documentation**: [hol03_v1.0.2.md](hol03_v1.0.2.md)

### Objectives

- HOL 3-1: Creating a Database Application from Scratch
- HOL 3-2: Creating a Database Application from a Spreadsheet
- HOL 3-3: Creating a Websheet Application

## Unit 4: Managing Pages in Page Designer

**Documentation**: [hol04_v1.0.2.md](hol04_v1.0.2.md)

### Objectives

- Creating a Dashboard
- Reviewing Page Designer
- Editing page components in Page Designer


## Unit 5:  Developing Reports

**Documentation**: [hol05_v1.0.2.md](hol05_v1.0.2.md)

### Objectives

- HOL 5-1: Creating a Classic Report
- HOL 5-2: Creating an Interactive Report
- HOL 5-3: Creating an Interactive Grid

## Unit 6:  Managing and Customizing Interactive Reports

**Documentation**: [hol06_v1.0.2.md](hol06_v1.0.2.md)

### Objectives

- HOL 6-1: Using an Interactive Report
- HOL 6-2: Customizing an Interactive Report as a Developer


## Unit 7:  Managing and Customizing Interactive Grids

**Documentation**: [hol07_v1.0.2.md](hol07_v1.0.2.md)

### Objectives

- HOL 7-1: Customizing the Team Members Interactive Grid
- HOL 7-2: Customizing the Projects Interactive Grid
- HOL 7-3: Customizing the Milestones Interactive Grid
- HOL 7-4: Customizing the Tasks Interactive Grid
- HOL 7-5: Customizing an Interactive Grid as a Developer
- HOL 7-6: Customizing an Interactive Grid as an End User


## Unit 8:  Creating and Using Forms

**Documentation**: [hol08_v1.0.2.md](hol08_v1.0.2.md)

### Objectives

- HOL 8-1: Updating the Form Pages in the Demo Projects Application
- HOL 8-2: Creating a Form on a Table and Linking a Report


## Unit 9:  Creating Application Page Controls

**Documentation**: [hol09_v1.0.2.md](hol09_v1.0.2.md)

### Objectives

- HOL 9-1: Updating the Team Members Pages
- HOL 9-2: Updating the Projects Pages
- HOL 9-3: Updating the Milestones Pages
- HOL 9-4: Updating the Tasks Pages

## Unit 10:  Adding Computations, Processes, and Validations

**Documentation**: [hol10_v1.0.2.md](hol10_v1.0.2.md)

### Objectives

- HOL 10-1: Implementing Validations on the Maintain Project Page
- HOL 10-2: Creating and Using a Computation
- HOL 10-3: Creating and Using a Process
- HOL 10-4: Creating and Using Validations


## Unit 11:  Implementing Navigation in your Application

**Documentation**: [hol11_v1.0.2.md](hol11_v1.0.2.md)

### Objectives

- Updating the Breadcrumb Entries for the Team Members, Projects, Milestones, and Tasks pages
- Updating the Navigation Menu Entries and Including Icons


## Unit 12:  Using Themes and Theme Styles

**Documentation**: [hol12_v1.0.2.md](hol12_v1.0.2.md)

### Objectives

- HOL 12-1: Updating the Navigation List
- HOL 12-2: Updating the Team Members Pages
- HOL 12-3: Creating and Using Theme Styles



## Unit 13:  Implementing Security in your Application 

**Documentation**: [hol13_v1.0.2.md](hol13_v1.0.2.md)

### Objectives

- Access Developer Cloud Service
- Complete Sprint
- Run Backlog and Sprint Reports
- Review Administrative Tasks


## Unit 14:  Adding Additional Pages to your Application 

**Documentation**: [hol14_v1.0.2.md](hol14_v1.0.2.md)

### Objectives

- Access Developer Cloud Service
- Complete Sprint
- Run Backlog and Sprint Reports
- Review Administrative Tasks


## Unit 15:  Creating and Using Dynamic Actions and Plug-ins

**Documentation**: [hol15_v1.0.2.md](hol15_v1.0.2.md)

### Objectives

- Access Developer Cloud Service
- Complete Sprint
- Run Backlog and Sprint Reports
- Review Administrative Tasks


## Unit 16:  Migrating Application Development Between Environments

**Documentation**: [hol16_v1.0.2.md](hol16_v1.0.2.md)

### Objectives

- Access Developer Cloud Service
- Complete Sprint
- Run Backlog and Sprint Reports
- Review Administrative Tasks
