# National Informatics Centre
eTransport Division

New Delhi
# Incident Report
Date Submitted: 26 Oct 2021

Created By: Kapil Jain

Keen & Able Computers Pvt. Ltd.

Noida
## Incident Description
eTransport Monitoring Team Member Shri Rahul Sharma reported the incident that Vahan Service URLs are critical in the Vahan Monitoring Whatsapp Group as below:-

10/25/2021 11:16 AM: Ràhul Sharma: https://vahan.parivahan.gov.in/vahanservice/vahan/ui/statevalidation/homepage.xhtml  url is critical


10/25/2021 11:20 AM: Ràhul Sharma: https://vahan.parivahan.gov.in/appointment/vahan/masterHome.xhtml  url is critical


10/25/2021 11:21 AM: Ràhul Sharma: https://vahan.parivahan.gov.in/appointment/vahan/ui/login/login.xhtml  url is critical

Vahan Service Portal went down and services were not available to the Users.
## Incident Date:  25 Oct 2021
## Severity: Critical
## Impacted Services:
Vahan Service and Appointment web applications were completely down. Vahan services for many states were impacted and were not available to the Users.
## Service Outage:
25 Oct 2021 @11:20 AM to 11:30 PM

26 Oct 2021 @9:30 AM to 1:30 PM
## Chronology of Incident:
25 Oct 2021 @11:16 AM – Incident reported in Whatsapp Group


25 Oct 2021 @11:30 AM – Shri Harsh Choudhary from Infra Support Team started investigating the issue and made attempts to restart the applications but it was always shutting down. 


Suspected that issue could be from Application side and tried to revert the application WAR deployed on 22 nd Oct night to the previous WAR of 18th Oct 2021. However couldnt revert and test because Application Team suspected that the issue is at the server side. The WAR was deployed on 22 Oct 2021 and it worked well till 11:20 AM of 25 Oct 2021. Saturday was working day for many states and there were normal load reported on Saturday.  

The application was stopping at the launch of Home Page itself. Infra Team suspected some dependencies stopping the app at Home Page, verified with Application Team but it was informed that there was no DB or API calls before login into the application. Hence analysis continued to look into server side logs and its troubleshooting.
25 Oct 2021 @12:30 PM – Considering the Severity and Impact of the Incident and initial assumption of being server side issue by Application Team, Shri Suresh Gupta apprised Shri Varad Gupta, and requested him to join the VC to guide and troubleshoot the issue. The VC was opened @12:30 PM and troubleshooting session continued till 5:30 PM but no root cause detected and applications were down.

In log analysis, Too many open files error was observed.  

 java.lang.IllegalStateException: java.io.FileNotFoundException: /u01/Deployment_Vahan/Tomcat_Instance/tcat_vahan_8080/webapps/vahanservice/WEB-INF/lib/poi-3.7.jar (Too many open files)
Caused by: java.io.FileNotFoundException: /u01/Deployment_Vahan/Tomcat_Instance/tcat_vahan_8080/webapps/vahanservice/WEB-INF/lib/poi-3.7.jar (Too many open files)

Increased Ulimit of open file for Tomcat User

application     soft    nofile          131072 earlier value was 65332
application     hard    nofile          131072 earlier value was 65332

Application was getting down within 10 min of start up. No root cause were identified so far.

25 Oct 2021 @5:30 PM – Shri Suresh Gupta again updated the status of investigation to Shri Avnesh Kumar Sharma and requested to invite Application & Database Team for Joint Troubleshooting as there were no major issue detected so far at server side. 

25 Oct 2021 @6:00 PM – VC opened for joint troubleshooting. Shri Avnesh Kumar Sharma, Smt. Ragyi Singh Yadav & Smt. Selvi joined the VC with Shri Varad Gupta & Shri Harsh Choudhary. The WAR file of 18 Oct 2021 was deployed and tested but no issue persisted.

The session continued till 9:30 PM where tcp dump was analysed and observed that a database query was taking more than 3 sec. The query was shared with DB Team to check. No root cause identified so far.

25 Oct 2021 @10:30 PM – VC resumed where Shri Ninad Shah from DB Team also joined. The application WAR was redeployed and observed it started working. Monitored it for 30 Min and then concluded that there was some strange issue which got resolve and decided to monitor the application closely. Informed the Monitoring Team and Joint Troublshooting Session closed @11:30 PM.

26 Oct 2021 @1:00 AM – Infra Team configured the JMX exporter for one Tomcat instance of vahanservice to monitor the JVM Metrics
 
26 Oct 2021 @9:30 AM – Shri Rahul Sharma from Monitoring Team reported the recurrence of the incident @9:31 AM

https://vahan.parivahan.gov.in/vahanservice/vahan/ui/statevalidation/homepage.xhtml  url is critical

[9:40 AM, 10/26/2021] Ràhul Sharma: https://vahan.parivahan.gov.in/appointment/vahan/masterHome.xhtml  url is critical

[9:41 AM, 10/26/2021] Ràhul Sharma: https://vahan.parivahan.gov.in/appointment/vahan/ui/login/login.xhtml  url is critical

26 Oct 2021 @9:45 AM – Shri Rahul Sharma called and informed about the URL not working to Shri Suresh Gupta who in turn, called up Shri Harsh Choudhary and Shri Varad Gupta. Shri Harsh Choudhary started looking into the issue. 
Informed about the recurrence of the incident to Shri Avnesh Kumar Sharma and requested to bring Application, Database and Network Team for joint troubleshooting. 

26 Oct 2021 @10:30 AM -  VC opened. As the issue was assumed to be related to server side error or network latency, Application and Databsse Team didnt join the VC and even Network Team also didnt join despite repeated requests. Gupta.

Infra Support Team continued to analyse the JVM Metrics captured thru JMX Exporter (implemented last night on one Tomcat Instance), made config params tweaking but application did not work.

26 Oct 2021 @11:30 AM – Shri Sanjay Mendiratta, Smt. Ragyi Singh Yadav and Smt. Selvi joined the VC. Shri Sanjay Mendiratta reviewed the logs, server parameters (ulimits) but no conclusions made. 
As the appliaction was stopping at the Home Page, Shri Sanjay Mendiratta & Shri Ninad Shah started checking the database logs and observed that large size db logs were generating. The DB Team found that it was due to vistors metrics DB Calls made during the launch of the Home Page. The vistor metrics is stored in RDBMS and it was taking longer time to retrieve/update the metric.   

26 Oct 2021 @12:30 PM : It was decided to comment the Visitor Metric portion in the source code, rebuild the WAR, deploy and test it.

26 Oct 2021 @1:30 PM: Updated WAR shared by Application Team which was deployed and tested. Found that application has started working and stable. Monitored the applications for 30 Min and made it open for the users at large. Informed the Monitoring Team to continue close monitoring. 

26 Oct 2021 @2:00 PM: The root cause suspected and verified was the db queries taking longer time and blocking the application during the launch of the Home Page itself.
## Analysis Made:
Server Logs analysed
- catalina.out
- access.log
- application.log
## Observations/Findings:
In Logs, following error was observed:-

- Too many files open error were detected   

 java.lang.IllegalStateException: java.io.FileNotFoundException: /u01/Deployment_Vahan/Tomcat_Instance/tcat_vahan_8080/webapps/vahanservice/WEB-INF/lib/poi-3.7.jar (Too many open files)
Caused by: java.io.FileNotFoundException: /u01/Deployment_Vahan/Tomcat_Instance/tcat_vahan_8080/webapps/vahanservice/WEB-INF/lib/poi-3.7.jar (Too many open files)
## Root Cause:
Visitors metrics - DB queries taking longer time to respond during launch of the Home Page for every User resulted in blocking the applications within a few minutes.
## Corrective Actions:
Comment the Visitors Metrics Database Calls in the source code, rebuild the WAR and deploy it.
## Preventive Actions/Recommendations:
Explore alternative method of storing/managing Visitors Metrics like Prometheus Database
References to explore & implement:-

> To push metrics from a piece of legacy java code, the following provides detailed instructions and examples => https://github.com/prometheus/client_java

> To query Prometheus (promQL) and display those (lets say on a page), the following provides examples => https://github.com/mckang/promql_java_client

App Monitoring - All applications expose a health API that can be monitored by the Monitoring Team

Change Management Process to track the changes and effect thru CI/CD 
Recommended to run Load/Performance Testing of the Applications for Production Deployment for early detection of any performance issues.
## Lessons Learnt:
During any critical production issue resulting in severe impact to the services availability, all concerned team should join the Troubleshooting Session as soon as the issue is reported even though the issue may be assumed to be Infra or Application or Network or DB issue in first sight, to ensure the analysis is made 360 degree to trace the root cause and apply the corrective action(s).
Perform Load/Performance Testing for every major release to keep track of the application performance and stability.
