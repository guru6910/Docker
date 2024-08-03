# ${\color{red} \textbf{Database}}$

### ${\color{green} \textbf{dockerfile}}$
````
FROM mariadb

LABEL database="springbackend"

ENV MARIADB_ROOT_PASSWORD=1234

COPY springbackend.sql /docker-entrypoint-initdb.d/

EXPOSE 3306
````
### ${\color{green} \textbf{init-database.sh}}$
````
#!/bin/bash
set -e

# Wait for MariaDB to start
sleep 10

# Check if the SQL file exists and is readable
if [ ! -r /Project-Angular-App/database/springbackend.sql ]; then
    echo "SQL file not found or not readable"
    exit 1
fi

mysql -h localhost -u root -ppasswd123 -e "CREATE DATABASE IF NOT EXISTS angularbackend;"

mysql -h localhost -u admin -ppasswd123 angularbackend < /Project-Angular-App/database/springbackend.sql

mysql -h localhost -u admin -ppasswd123 -e "USE angularbackend; SHOW TABLES;"

mysql -h localhost -u admin -ppasswd123 -e "USE angularbackend; DESCRIBE tbl_workers;"

````

# ${\color{red} \textbf{Backend}}$

### ${\color{green} \textbf{dockerfile}}$
````
FROM ubuntu:latest

# Install dependencies
RUN apt-get update && \
    apt-get install -y openjdk-8-jdk maven git && \
    rm -rf /var/lib/apt/lists/*

# Set the working directory in the container
WORKDIR /app

# Clone the project repository
RUN git clone https://github.com/guru6910/Project-Angular-App.git

# Change directory to the backend project
WORKDIR /app/Project-Angular-App/spring-backend

# Copy the application properties file (assuming you have it in the same directory as the Dockerfile)
COPY application.properties src/main/resources/application.properties

# Build the Maven project
RUN mvn clean package -DskipTests

# Expose the port the application runs on
EXPOSE 8080

# Command to run the application
CMD ["java", "-jar", "target/spring-backend-v1.jar"]
````

### ${\color{green} \textbf{application.properties}}$
````
spring.datasource.url=jdbc:mysql://172.17.0.2:3306/angularbackend?useSSL=false
spring.datasource.username=admin
spring.datasource.password=passwd123

spring.jpa.generate-ddl=true
````

# ${\color{red} \textbf{Frontend}}$

### ${\color{green} \textbf{dockerfile}}$
````
FROM ubuntu:latest

RUN apt update -y 

RUN apt install -y git

RUN git clone https://github.com/guru6910/Project-Angular-App

WORKDIR /Project-Angular-App/angular-frontend

RUN apt install nodejs npm -y

RUN npm install -g @angular/cli@14.2.1

COPY worker.service.ts src/app/services/worker.service.ts

RUN npm install

RUN ng build

EXPOSE 80

CMD ["ng", "serve", "--host", "0.0.0.0", "--port", "80"]
````

### ${\color{green} \textbf{worker.service.ts}}$

````
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { Worker } from '../models/worker';

@Injectable({
  providedIn: 'root'
})
export class WorkerService {

  private getUrl: string = "http://172.17.0.3:8080/api/v1/workers";

  constructor(private _httpClient: HttpClient) { }

  getWorkers(): Observable<Worker[]> {
    return this._httpClient.get<Worker[]>(this.getUrl).pipe(
      map(response => response)
    )
  }

  saveWorkers(worker: Worker): Observable<Worker> {
    return this._httpClient.post<Worker>(this.getUrl, worker);
  }

  getWorker(id: Number): Observable<Worker> {
    return this._httpClient.get<Worker>(`${this.getUrl}/${id}`).pipe(
      map(response => response)
    )
  }

  deleteWorker(id: Number): Observable<any> {
    return this._httpClient.delete(`${this.getUrl}/${id}`, {responseType: 'text'});
  }
}
````
