# Introduction 

*Norfolk Cache* is a simple key-value storage application designed for Azure Mentoring program. This guide contains a bunch of practical tasks that will help mentees successfully complete the program.

# Getting Started

Fork this repository to create your own copy that you will use in this program. 


## Project Structure

- [NorfolkCache](NorfolkCache) - an ASP.NET MVC application that runs on .NET Framework version 4.6.1.
- [ApiTests](ApiTests) - a set of API tests that are built using chakram/mocha.
- [LoadTests](LoadTests) - a set of load tests and initial data. 


## Environment Branches

- **master** - a stable production-ready branch for UAT environment.
- **development** - an unstable development branch for Continuous Integration environment (create in your repository).
- **release** - a stable release branch for Production environment (create in your repository).

Learn more about ["Environments in Release Management"](https://docs.microsoft.com/en-us/vsts/build-release/concepts/definitions/release/environments).


## Development Workflow

- Developers work on a feature using it's own feature branch.
- When the feature is completed a responsible developer merges the feature branch to **development** branch.
- When the feature is tested and verified by QA on Continuous Integration environment the feature changes are merged to master branch.
- When the feature is tested and verified by the UAT acceptance group the feature changes are merged to the release branch.  


# Build and Test

Open ASP.NET MVC solution file [NorfolkCache](NorfolkCache\NorfolkCache.sln) with Visual Studio 2015 or Visual Studion 2017.


## Run API tests

Install NodeJS, and run the commands from NodeJS command prompt:

```sh
$ cd ApiTests
$ npm install
$ rem Replace host in urlBase with relevant environment host in test\tests.js. 
$ notepad test\tests.js
$ npm test
```


# 1. Practical Task "Web Apps"

The goal of this task is to create a set of App Services for three different environments:
- *my-norfolk-cache-ci* - a unstable CI environment with the latest version of **development** branch.
- *my-norfolk-cache-uat* - a stable UAT environment with the latest version of **master** branch.
- *my-norfolk-cache* - a stable production environment with the latest version of **release** branch.

### Please, use your own prefix name instead of *my* to avoid name conflicts.


## Scenario "Abigail"

1. Create a new resource group *my-norfolk-cache*. (["Azure Resource Manager"](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).)

2. Create a new *my-norfolk-cache-ci* web app in *my-norfolk-cache* resource group with new app service plan *my-norfolk-cache-ci-plan* (free pricing tier, any location).

3. Create a new *my-norfolk-cache-uat* web app in *my-norfolk-cache* resource group with new app service plan *my-norfolk-cache-uat-plan* (free pricing tier, any location).

4. Create a new *my-norfolk-cache* web app in *my-norfolk-cache* resource group with new app service plan *my-norfolk-cache-plan* (free pricing tier, any location).

5. Setup deployment for *my-norfolk-cache-ci* web app:
	* Open "Deployment slots" tab and learn information on this page.
	* Open "Deployment options" tab and add github deployment for **development** branch. Wait until the app will be deployed.
	* Open "Activity log" and find "Update website" operation in the log.

6. Gain more information about your *my-norfolk-cache-ci*:
	* Open "Overview" tab and open a web app url - http://my-norfolk-cache-ci.azurewebsites.net.
	* Learn about "Quotas" for this web app. (["Monitor Apps"](https://docs.microsoft.com/en-us/azure/app-service/web-sites-monitor).)
	* Run API tests for this host and learn how this affect quotas.

7. View detailed information about processes:
	* Open "Process explorer" and learn more about w3wp processes: PID, threads, working set, private memory, open handles, loaded modules.
	* Open Site Configuration Console (SCM) or Kudu for this web app - https://my-norfolk-cache-ci.scm.azurewebsites.net/. (["Accessing the Kudu Service"](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).)
	* Use REST API to get information about all processes - https://my-norfolk-cache-ci.scm.azurewebsites.net/api/diagnostics/processes/.
	* Use REST API to get information about a specific process - https://my-norfolk-cache-ci.scm.azurewebsites.net/api/diagnostics/processes/process-id.

8. View diagnostics logs:
	* Open "Diagnostics logs" and enable filesystem application (Information level) and web server logging, and detailed error messages. ([Learn about troubleshooting a web app in Azure](https://docs.microsoft.com/en-us/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio), [Load test](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test).)
	* Open "Log stream" go to "Application logs" and run API tests.

```
2017-10-31T12:44:39  PID[5944] Information CacheService.TryGet() enter
2017-10-31T12:44:39  PID[5944] Information CacheService.TryGet() exit
2017-10-31T12:44:39  PID[5944] Information CacheService.TryGetNamespaceKeys() enter
2017-10-31T12:44:39  PID[5944] Information CacheService.TryGetNamespaceKeys() exit
```

	* Open "Log stream", go to "Web server logs" and run API tests. (["Enable diagnostics logging"](https://docs.microsoft.com/en-us/azure/app-service/web-sites-enable-diagnostic-log).)
	* Open Kudu, download a web app diagnostic dump using Tools->Diagnostic dump.

9. Restarting the web app:
	* Run API tests, and open the web app url.
	* Restart the app, and open the web app url.
	* Run API tests, and open the web app url.
	* Kill IIS w3wp process, and open url.

10. Using console:
	* Open "Console" and go to D:\home\site\wwwroot folder. Create a new "my-test" folder.
	* Open CMD Debug Console from Kudu console in an another browser tab and go to D:\home\site\wwwroot folder. Find "my-test" folder in the current folder.
	* Edit Web.config, and add customErrors section:
```xml
<configuration>
    <system.web>
        <customErrors mode="Off"/>
    </system.web>
</configuration>
```

11. Setup *my-norfolk-cache-uat* web app.
	* Use Github's **master** branch for deployment.
	* Enable application (Warning, filesystem) and web server logging.
	* Review web app quotas.
	* Run API tests for this environment.

12. Setup *my-norfolk-cache* web app.
	* Use Github's **release** branch for deployment.
	* Enable application logging (Error) and web server logging. Use *mynorfolkcachelogs* blob as a storage (use *applogs* and *webserverlogs* as a container names for application and server logs).
	* Review web app quotas.
	* Run API tests for this environment.

13. Export Azure Resource Manager template:
	* Open "Automation script" for *my-norfolk-cache* resource group.
	* Send *template.json* (rename to Abigail.json) file to your mentor.

14. Setup performance testing for UAT environment:
	* Open "Deployment options", and enable Performance Test (VSTS account is needed). Test parameters are:
		* Url: http://my-norfolk-cache-uat.azurewebsites.net/api/cache/namespaces
		* Performance Test name: PerfTestMyNorfolkCacheUat.
		* User Load: 40
		* Duration: 1
	* Synchronize your web app with repository after this changes. Wait.
	* Open "Overview" - "Requests", "Average Response Time" and "Data Out" charts.
	* Open "Deployment options" and click on deployment details. Click on "View Log" link for "Performance Test" activity. Learn messages, and test results.
	* Open "Performance test" and on the test run - you will get on the same page.
	* Open your VSTS account, click on "Test" menu item, then "Load test".

15. Export the resource group ARM template, and send it (AbigailPerf.json) to your mentor.

16. Verify your pipeline by introducing a new change that is easy to observe on the web app UI.
	* Commit to *development* branch, observe changes on CI environment.
	* Merge changes to *master* branch, observe changes on UAT environment.
	* Inspect results of your latest performance test.
	* Merge changes to *release* branch, observe changes on Production environment.

17. Delete *my-norfolk-cache* resource group.


## Scenario "Brooklynn"

1. Create a new resource group *my-norfolk-cache*, a new *my-norfolk-cache* web app with new app service plan *my-norfolk-cache-plan* (free tier).

2. Open "Scale up (App Service plan)", and scale the plan up to "S1 Standard". Open "App Service plan" and make sure that those changes are applied.

3. Open "Deployment slots", and add a new slot - *ci*. Setup "Deployment option" for this slot - *development* branch on Github. Run API tests on [this environment](https://my-norfolk-cache-ci.azurewebsites.net/) after deployment.

4. Open "Deployment slots", and add a new slot - *uat*. Setup "Deployment option" for this slot - *master* branch on Github. Run API tests on [this environment](https://my-norfolk-cache-uat.azurewebsites.net/) after deployment.

5. Open "Deployment slots", and add a new slot - *release*. Setup "Deployment option" for this slot - *release* branch on Github. Run API tests on [this environment](https://my-norfolk-cache-release.azurewebsites.net/) after deployment.

6. Open https://my-norfolk-cache.azurewebsites.net, and make sure that this web app is not deployed.

7. Swap *release* and *production* deployment slots. Check out [release](https://my-norfolk-cache-release.azurewebsites.net/) and [production](https://my-norfolk-cache.azurewebsites.net/) environments to make sure that the slots are swapped.

8. Open "Deployment option" for *release* slot, and sync the slot deployment. Make sure that [release](https://my-norfolk-cache-release.azurewebsites.net/) environment is synchronized.

9. Export the resource group ARM template, and sent *template.json* (Brooklynn.json) to your mentor.

10. Delete *my-norfolk-cache* resource group.


## Quest "Clarissa"

1. Create a new web app, and setup deployment to "Local Git Repository". Add *NorfolkCache* folder, and deploy the application.

2. Add existed *NorfolkCache.SpecialServices* project to *NorfolkCache* solution, reference the project from *NorfolkCacheWebApp* and modify *DependencyConfig* to register *MyService* (uncomment code after TODO).

3. Commit, push.

4. Open "Deployment options", and make sure that the build failed.

5. Investigate an error both using Azure Portal and Kudu console.

6. Fix an error by changing *NorfolkCache.SpecialServices* project file. Redeploy, and make sure that the application is deployed successfully.


## Questions

1. What is a resource group location?
2. What plan do you need to have deployment slots for your web app?
3. How to access Kudu or SCM for a web app?