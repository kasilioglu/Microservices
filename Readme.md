### About

Most basic microservices demonstration with .Net Core 5.0, build with [Julio Casal's online course](https://www.udemy.com/course/building-microservices-with-net-the-basics/). Focusing on async service communication. Demonstration of;

* A service template (Play.Common project) available as a private nuget package

* Mongo db as a repository. Generic repository pattern implemented at service template. All services have their own databases.

* Inter-service communication handled via events. Mass transit is used to encapsulate rabbit-mq.

### Before running - Create local nuget packages

A common library has been created to use at other services (and message contracts). Therefore you should first setup a local nuget package source.

This is just for a single developer's environment. Normaly you and your team should have a shared private package repository (possibly cloud).
  

##### To pack Play.Common project;

Switch to ***Play.Common/src/Play.Common*** folder and run the following command at terminal.

    dotnet pack -p:PackageVersion=1.0.1 -o C:\projects\localNugetSource\packages\


##### To pack Play.Catalog.Contracts project;

Switch to ***Play.Catalog\src\Play.Catalog.Contracts*** folder and run the following command at terminal.

    dotnet pack -o C:\projects\localNugetSource\packages\

  

Run the following command at the terminal to point new nuget source to nuget cli. Now your services can reach out this local repo and its package contents.

    dotnet nuget add source C:\projects\localNugetSource\packages -n LocalPackages

Note that this is not specific to this project but your package manager now will also check this repo for any of your projects, so you can pack anything to this location.
 
### How to Run
 In order to run this project, you should first ensure docker components are up and running. Then it's all about running .Net Core services. Follow these steps;

##### Docker 
Switch to ***Play.Infra*** folder and run the following at terminal, this will make rabbit-mq and mongo db containers running.

    docker-compose up

##### Catalog service
Switch to ***Play.Catalog\src\Play.Catalog.Service*** folder and run following commands;

    dotnet build 
    dotnet run
 
##### Inventory service
Switch to ***Play.Inventory\src\Play.Inventory.Service*** folder and run following commands;

    dotnet build
    dotnet run

  
##### Check all fine
At this point swagger ui and rabbit-mq management portal should be up.

Catalog service : https://localhost:5001/swagger/index.html

Inventory service : https://localhost:5005/swagger/index.html

Rabbit-MQ          : http://localhost:15672/ (user:guest, pwd:guest)
  
### How to Test

Test scenario is posting an item via Inventory service. The expected result is;

 - Catalog service creates an item at catalog database (Catalog.items)
   and publishes a CatalogItemCreated event.
 - Inventory service consumes event and creates a copy of item at its
   own database (Inventory.catalogItems).

To run this scenario, switch to postman collection ***Play.Catalog.Service***. And run Post request. Then, you should see the item is created at both databases.