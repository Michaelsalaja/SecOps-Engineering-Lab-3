# Connect Linux Hosts To Microsoft Sentinel Using Data Connectors

In my most previous lab, I created a Windows Virtual Machine in Azure and connected an Azure Windows Virtual Machines to Microsoft Sentinel workspace on Microsoft Defender XDR. This lab switches to connecting Linux hosts to Microsoft Sentinel using the Common Event Format (CEF) via Azure Monito Agent (AMA) and Syslog via Syslog via Legacy Agent data connectors. I will also create a Data Collection Rule (DCR) to collect the events while Azure Arc was pre-installed on this Linux host as required for creating a DCR.

The diagram below depicts the architectural design of what my lab wants to operationalize, following three tasks.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%201.png)

Figure 1: Architectural design diagramm

## Task 1

## Connect a Linux Host using the Common Event Format connector

Step 1

I directly accessed the Microsoft Sentinel Workspace with my tenant email, given the fact that Microsoft Sentinel has been predeployed in my Azure subscription with the name sentinelworkspace-01, and the required Content Hub solutions have been installed

Step 2

* I launched a separate virtual machine named LIN1 and entered my credentials to note down my IP address.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%202.png)

Figure 2: LIN1 Virtual Machine

* I headed back to my Win1 to start the Microsoft Edge browser.

* I launched Windows PowerShell by typing Windows PowerShell in the Search form in the Taskbar, and then selected Windows PowerShell

* Adjusting for my specific Linux server information, I entered the following PowerShell command:

`ssh 192.168.1.31 -l`

* I entered "Yes" to confirm the connection to the host, and entered my password

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%203.png)

Figure 3: Screenshot of a Windows PowerShell terminal showing an SSH connection to an Ubuntu server.

* In the SSH session, at the Linux prompt, I ran the following command to connect my ResourceGroup1 with the Azure Arc:

`azcmagent connect -g "ResourceGroup1" -l "CentralUS" -s "07690f6e-4e45-4612-bd0c-879dbf4a129c"`

* The azcmagent command helps to configure, manage, and troubleshoot a server's connection with Azure Arc.

* I was provided with a link to open and a code to enter in the browser, which will connect the machine to Azure. ([https://microsoft.com/devicelogin](https://microsoft.com/devicelogin))

 ![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%204.png)
 
 Figure 4: Screenshot of Windows PowerSHell Commandline interface
 
 * I entered the code and chose my tenant credential to connect

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%205.png)

Figure 5: Screenshot of a browser showing tenant credential

 * The first attempt to connect the machine to Azure failed, so I repeated the same steps
 * I got a confirmation that the connection was successful 

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%206.png)

Figure 6: Screenshot of a failed connection to Azure

 * A confirmation for the successful connection popped up in the command line prompt

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%207.png)

Figure 7: Screenshot of a terminal window showing Azure Arc agent connection

* I ran the command below to confirm in the command output that the Agent status was connected successfully.

<mark>azcmagent show</mark>

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%208.png)

Figure 8: Screenshot of a terminal window showing Azure Arc agent connection and status on a Linux machine


## Step 2

* Headed back to the Microsoft Edge browser.

* Navigated to Defender XDR at <mark>https://security.microsoft.com</mark>

* In the Microsoft Defender navigation menu, scrolled down and expanded the Microsoft Sentinel section.

* Among the 4 sections Threat Management, Content Management, Configuration, Data Lake Exploration, I expanded the Content Management section and selected Content Hub


![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%209.png)

Figure 9: Screenshot of Microsoft Defender Home page showing unified SIEM and XDR dashboard with SOC optimization, Entity Behavior Analytics, and Microsoft Sentinel data connectors.

* In the Content hub, I searched for the Common Event Format solution and selected it from the list.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2010.png)

Figure 10: Screenshot of Microsoft Defender Content hub showing search results for "Common Event Format" with various solutions listed and the "Common Event Format" solution details pane open on the right.

* The Common Event Format solution package installs both the Common Events Format (CEF) via AMA and the Common Events Format (CEF) via Legacy Agent Data connectors.

* Selected the *Common Events Format (CEF) via AMA* Data connector, and selected **Open connector page** on the connector information blade.


![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2011.png)

Figure 11: Screenshot of Microsoft Defender portal showing Common Event Format solution details in Content hub


* In the *Configuration* section, I scrolled selected the **+Create data collection rule** button.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2012.png)

Figure 12: Screenshot of Microsoft Defender portal showing Common Event Format (CEF) via AMA connector details, including prerequisites, table management, and configuration sections with a list of data collection rules.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2013.png)

Figure 13: Screenshot of Microsoft Defender portal showing the bottom portion of the data connector page, including the "+ Create data collection rule" button and a command line instruction for installing the CEF collector.

* On the Create data collection rule page, Basic tab, I entered <mark>AZLIN63218350DCR</mark> for Rule Name, then selected Next: Resources.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2014.png)

Figure 14: Screenshot of Microsoft Defender portal showing the DCR Wizard on the Basic tab. A red arrow points to the Rule name field where AZLIN62636865DCR is entered. A red box highlights the "Next: Resources >" button at the bottom right.

* On the resource tab, I expanded my MOC Subscription under Scope.

* I expanded ResourceGroup1, then select ubuntu1.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2015.png)

Figure 15: Screenshot of Microsoft Defender for Cloud Sentinel Data Connector page showing DCR Wizard

* selected the *LOG_ALERT* drop-down menu, and selected **LOG_WARNING**.

* Selected **Next: Review + create**, and selected **Create** and waited for the deployment to finish.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2016.png)

Figure 16: Screenshot of Microsoft Defender for Cloud DCR Wizard configuration

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2017.png)

Figure 17: Screenshot of Microsoft Defender for Cloud DCR Wizard validation page


* The *Common Event Format (CEF)* via AMA Data connector showed **Connected**

* The data collection rule installs the Azure Monitor Agent (AMA), and the *CEF collector* install commands were pre-deployed on the LIN1 machine to install the CEF collector.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2018.png)

Figure 18: Screenshot of Microsoft Defender portal showing Data Collection Rule (DCR) wizard and a list of existing DCRs.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2019.png)

Figure 19: Screenshot of Microsoft Defender portal showing Common Event Format (CEF) via AMA connector details, including table management and a list of data collection rules.

* Returned to the PowerShell Command Prompt window. On my LIN1 virtual machine and ran the following command:

<u>netstat -lnptv</u>

* The output showed that the rsyslog (or syslog-ng) daemon listening on port 514.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2020.png)

Figure 20: Screenshot of a terminal window showing Azure machine connection details and netstat output. The rsyslogd process is shown listening on port 514.


### Task 2

## Connect a Linux host using the Syslog connector

Step 1

* Launched another LIN2 virtual machine.

* Logged in with my username and password and noted the IP address shown in the output

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2021.png)

Figure 21: Screenshot of a terminal window showing a successful login to Ubuntu 18.04.6 LTS.


* I returned back to WIN1 virtual machine, re-launched the Windows PowerShell, and entered the following command in the command line prompt:

`Ssh 192.168.1.32 -l root`

* I entered yes to confirm the connection and then typed the user's password

* In the SSH session, at the linux prompt, I ran the following command.

`azcmagent connect -g <mark>"ResourceGroup1"</mark> -l <mark>"CentralUS"</mark> -s <mark>"91c2ad4e-ba7e-43c6-bf9a-e826ad584b1b"</mark>`

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2022.png)

Figure 22: Screenshot of a terminal window showing an Ubuntu SSH session


* I was provided with a link to open and a code to enter in the browser, which will connect the machine to Azure. ([https://microsoft.com/devicelogin](https://microsoft.com/devicelogin))

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2023.png)


Figure 23: Screenshot of a link and code for signin on the Windows PowerShell

* Pressed Command and Enter on my Macbook’s keyboard and clicked the Microsoft Edge link

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2024.png)

Figure 24: Screenshot of a login page


* I entered the code and chose my tenant credential to connect

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2025.png)


Figure 25: Screenshot of a web browser showing the Microsoft device login page.

* The first attempt to connect the machine to Azure failed, so I repeated the same steps and

* I got a confirmation that the connection was successful

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2026.png)

Figure 26: Screenshot of a web browser showing a Microsoft sign-in prompt for Azure Connected Machine Agent.

* I got a confirmation for successful connection

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2027.png)

Figure 27: Screenshot of a terminal window showing the process of connecting an Ubuntu machine to Azure using azcmagent.


* I ran the command below to confirm in the command output that the Agent status was connected successfully.

<mark>azcmagent show</mark>

* The Agent status was successfully connected.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2028.png)

Figure 28: Screenshot of a terminal window showing the output of azcmagent commands, confirming the agent status as Connected.

* I navigated to Microsoft Defender Portal on the Microsoft Edge browser.

* Navigated to Defender XDR at <mark>https://security.microsoft.com</mark>

* In the Microsoft Defender navigation menu, I scrolled down and expanded the Microsoft Sentinel section.

* Among the 4 sections Threat Management, Content Management, Configuration, Data Lake Exploration, I expanded the Content Management section and selected Content Hub

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/592915136e497acc9a94d7490dfd004edb2fbb7c/Figure%2030A.png)

Figure 29: Screenshot of Microsoft Defender Home page showing the SOC optimization dashboard and navigation menu.

* In the Content hub, I searched for the Syslog solution and selected it from the list.

* Clicked on “Manage” on the Syslog solution page

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/4a30de0841057c87708059cc750fb281a60afca1/Figure%2029.png)

Figure 30: Screenshot of Microsoft Defender Content hub page showing the Syslog solution details and a list of installed content items.


* The Syslog solution package installs two Syslog Data connector, 7 Analytics rules, 9 Hunting queries, 2 Parsers and 21 Workbooks.

* Selected *Syslog via AMA* Data connector from the options, and selected **Open connector page** on the connector information blade

* I ran into some difficulty as the page below shows that Syslog via AMA remains disconnected

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/592915136e497acc9a94d7490dfd004edb2fbb7c/Figure%2031.png)

Figure 31: Screenshot of Microsoft Defender portal showing Syslog solution details and Syslog via AMA connector status as disconnected.


* I repeated the task to verify if I missed anything and proceeded to select +Create data collection rule in the Configuration section

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/592915136e497acc9a94d7490dfd004edb2fbb7c/Figure%2032.png)

Figure 32: Screenshot of Microsoft Defender portal showing the Data Connector page for Syslog via AMA

* On the Create data collection rule page, Basic tab, enter AZLIN63220887DCR2 for Rule Name, then select Next: Resources.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/592915136e497acc9a94d7490dfd004edb2fbb7c/Figure%2033.png)

Figure 33: Screenshot of the DCR Wizard in Microsoft Defender. The "Basic" tab is selected


* On the Resources tab, I expanded your MOC Subscription under Scope.

* Expanded **ResourceGroup1**, then selected **ubuntu2**.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/592915136e497acc9a94d7490dfd004edb2fbb7c/Figure%2034.png)

Figure 34: Screenshot of Microsoft Defender portal showing the DCR Wizard on the Resources tab

* Selected **Next: Collect**.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/592915136e497acc9a94d7490dfd004edb2fbb7c/Figure%2035.png)

Figure 35: Screenshot of Microsoft Defender DCR Wizard showing resource selection

* In the *Collect* tab, I selected the **LOG_ALERT** drop-down menu, and select **LOG_WARNING**

* **Selected Next: Review + create**

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/592915136e497acc9a94d7490dfd004edb2fbb7c/Figure%2036.png)


Figure 36: Screenshot of Microsoft Defender DCR Wizard showing log collection configuration with LOG_WARNING selected

* I selected Create and the notification of "validation passed" was shown

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/592915136e497acc9a94d7490dfd004edb2fbb7c/Figure%2037.png)


Figure 37: Screenshot of Microsoft Defender portal showing the DCR Wizard with a "Validation passed" message and the "Create" button highlighted.

* The *Syslog via AMA* Data connector was **connected** successfully

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/592915136e497acc9a94d7490dfd004edb2fbb7c/Figure%2038.png)


Figure 38: Screenshot of Microsoft Defender portal showing the DCR Wizard with "Create Data Collection Rule Succeeded" and "Create Data Collection Rule Association Succeeded" notifications.

# Summary

In this lab, I executed the following tasks:

* Accessed the Microsoft Sentinel Workspace

* I connected a Linux Host using the Common Event Format connector through the Content Hub solution under the Microsoft Sentinel section in the Microsoft Defender portal

* Connected a Linux host using the Syslog via AMA Data connector

* Created Data Collection Rule which installed the Azure Monitor Agent (AMA)

* Configured the facilities to define the source of origin of the log messages

* Configured the severity for the Syslog connector to Level 4 (Log-Warning) to help us define the priority or severity of log messages.

* The syslog connection did not work perfectly, and it forced to troubleshoot a couple of errors that occurred during the configuration. However, I was able to understand how to navigate through the Microsoft Defender portal and connect configure Syslog and Common Event Format (CEF) data connectors and Microsoft Sentinel. This includes setting up the necessary infrastructure and ensuring that logs are correctly being ingested into Sentinels such as

    * CIS log forwarder

    * Azure Monitor Agent as well as

    * Data Connector Rules (DCR) and log forwarding.

![image alt](https://github.com/Michaelsalaja/SecOps-Engineering-Lab-3/blob/8f9b44096c8ed6625b56bfa72c5c47e519abf379/Figure%2039.png)

Figure 39: diagram illustrating on-premises systems sending Syslog data to a dedicated Azure VM running the Microsoft Sentinel agent.
