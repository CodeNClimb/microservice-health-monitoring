# USER INFORMATION SERVICE WITH OBSERVABILITY AND MONITORING METRICS
## About the Service
This repository contains a simple springboot microservice that retrieves user 
information based on the id specified in the url field. 
It uses the KONG API GATEWAY to abstract away the microservice itself, 
such that all requests are made to
the Kong Gateway which then redirects it to the springboot application.
The Kong API GUI can be accessed [here](http://localhost:8002) once the application is running.
The data for the application was generated from [Mockaroo](https://www.mockaroo.com/) and is available as a CSV at `user-info-service/src/main/resources/MOCK_DATA.csv`
This data automatically populates the database when the application starts.

## REQUIRED SOFTWARE
- An IDE ( [Visual Studio Code](https://code.visualstudio.com/download) or [intellij IDEA](https://www.jetbrains.com/idea/download/?source=google&medium=cpc&campaign=APAC_en_AU_IDEA_Branded&term=intellij+idea&content=602143185772&gclid=EAIaIQobChMI-f3uuYnegwMVwqRmAh0_ewXKEAAYASABEgImY_D_BwE&section=windows) )
- [Docker](https://www.docker.com/products/docker-desktop/)
- [Postman API](https://www.postman.com/downloads/)
- A Web Browser ( [Google Chrome](https://www.google.com/chrome/) or [FireFox](https://www.mozilla.org/en-US/firefox/new/))
- [Java](https://www.oracle.com/java/technologies/downloads/) 17 at minimum installed on your local machine.
- [Git](https://www.git-scm.com/downloads)
- [Apache Maven](https://maven.apache.org/)

## Setting Up - CLONE THE PROJECT
- Open a terminal or command prompt (Powershell on Windows and Terminal or linux or macOS)
- Navigate to the directory in which you want to clone the project as shown below
```powershell
cd path/to/your/directory
```
- Run 
```powershell
git clone git@github.com:msackey-IW/user-info-service.git
```
- Alternatively, in the [project repository](https://github.com/msackey-IW/user-info-service-observability) you can navigate to `code -> Open with GitHub Desktop` and clone the repository through GitHub Desktop.
- Run the code snippet below in the terminal.
```powershell
cd user-info-service
```
- Run the code snippet below in the terminal based on your operating system.
```powershell
<# Linux/MacOs #>
mvn clean package -DskipTests
OR
./mvnw clean package -DskipTests

<# Windows #>
mvn clean package -DskipTests
OR
.\mvnw clean package -DskipTests 

```
The above packages the springboot application into a .jar file.

- Run the code snippet below in your terminal.
```powershell
docker-compose up -d
```
The above creates and runs the microservice from the .jar file in detached mode.
- The endpoints are exposed via a kong gateway through the following url 
```curl
http://localhost:8000/user/{id}
```
- The id is field is a dynamic resource and returns all information attached to the user with the specified id.
- The database can be accessed through [PGADMIN4](http://localhost:5050). The default username and password for this service is available at `user-info-service/docker-compose.yaml` associated with environment variables "PGADMIN_DEFAULT_EMAIL" and "PGADMIN_DEFAULT_PASSWORD".
- The default login credentials for the database are available in `user-info-service/docker-compose.yaml`

## KONG API GATEWAY
- The application comes with a preloaded kong service to retrieve user information. 
- However, more services may be attached as required. Visit the Kong [official docs](https://docs.konghq.com/gateway/latest/get-started/services-and-routes/) to learn more about creating services and routes.
- The Kong GUI can accessed via the url below.
```curl
http://localhost:8002
```
- All KONG related services are available via the url below.
 ```curl
 http://localhost:8001/services
 ```
- Routes related to a service may be viewed at the url below
```curl
http://localhost:8001/services/YOUR-SERVICE-NAME/routes
```
## TESTING THE APPLICATION
- Open Postman API
- Create a new "GET Request" by navigation to `new -> Http -> GET`.
- In the url tab, enter 
```curl
http://localhost:8000/user/{id}
``` 
where the id is a dynamic integer referencing the user id to be retrieved.
- The id field must be a valid positive integer
- A sample request to `http://localhost:8000/user/1` will return the output below
```json
{
    "id": 1,
    "firstName": "Teodorico",
    "lastName": "Skaife d'Ingerthorpe",
    "age": 18,
    "sex": "Male"
}
```

## APPLICATION MONITORING
This application comes equipped prometheus and grafana to ensure you can keep tabs on the health of your application. 

### Prometheus
Prometheus is a monitoring tool used to track the health of your application.
Health metrics can be viewed using the link below.
```curl
http://localhost:9090
```

### Grafana
Grafana allows users to create dashboards and visualizations that make tracking the health metrics of an application convenient and easily accessible.

The default login details are shown below
```json
{
    "Email or username": "admin",
    "Password": "admin"
}
```
Change these default values for a more secure application.
Grafana is accessible using the url below
```curl
http://localhost:3000
```
To view a sample dashboard, login to grafana with the above credentials. <br/>
Creating a dashboard requires attaching a datasource but in this case, one has already been automatically configured. The configuration file is available at `user-info-service/config/grafana-datasource.yml`
<br/>Once on the homepage, navigate to `menu-> dashboards -> new -> import -> upload json dashboard JSON file`.
The JSON file is located at `user-info-service/config/grafana-dashboard.json`. <br/>
You should now be able to view the api_calls_total metric which count how many API calls have been made to the service in the past hour. <br/>
![grafana image](https://github.com/msackey-IW/user-info-service-observability/blob/main/assets/grafana-dashboard.jpg)
Visit the [grafana docs](https://grafana.com/docs/grafana/latest/getting-started/build-first-dashboard/) to learn how to create your own custom dashboards.

## CLOSING UP
Run the snippet below in the terminal.
```powershell
docker-compose down
```

This will shut down all docker containers and images related to this build.
