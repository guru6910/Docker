# ${\color{red} \textbf{Database}}$

### ${\color{green} \textbf{dockerfile}}$
````
FROM mariadb

LABEL database="springbackend"

ENV MARIADB_ROOT_PASSWORD=1234

COPY springbackend.sql /docker-entrypoint-initdb.d/

EXPOSE 3306
````
### ${\color{green} \textbf{springbackend.sql}}$
````
-- phpMyAdmin SQL Dump
-- version 5.1.1
-- https://www.phpmyadmin.net/
--
-- Host: 127.0.0.1
-- Generation Time: Oct 13, 2022 at 04:28 AM
-- Server version: 10.9.3-MariaDB
-- PHP Version: 7.4.27

SET SQL_MODE = "NO_AUTO_VALUE_ON_ZERO";
START TRANSACTION;
SET time_zone = "+00:00";


/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8mb4 */;

--
-- Database: `springbackend`
--

-- --------------------------------------------------------

--
-- Table structure for table `tbl_workers`
--

CREATE TABLE `tbl_workers` (
  `id` bigint(20) NOT NULL,
  `status` varchar(255) DEFAULT NULL,
  `workerfname` varchar(255) DEFAULT NULL,
  `workerlname` varchar(255) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

--
-- Dumping data for table `tbl_workers`
--

INSERT INTO `tbl_workers` (`id`, `status`, `workerfname`, `workerlname`) VALUES
(1, 'Working', 'Ivan', 'Holicek'),
(37, 'Vacation', 'Marko', 'Markovic'),
(40, 'Working', 'Ivo', 'Ivica'),
(41, 'Working', 'Luka', 'Lukovic'),
(42, 'Working', 'Filip', 'Filipovic');

--
-- Indexes for dumped tables
--

--
-- Indexes for table `tbl_workers`
--
ALTER TABLE `tbl_workers`
  ADD PRIMARY KEY (`id`);

--
-- AUTO_INCREMENT for dumped tables
--

--
-- AUTO_INCREMENT for table `tbl_workers`
--
ALTER TABLE `tbl_workers`
  MODIFY `id` bigint(20) NOT NULL AUTO_INCREMENT, AUTO_INCREMENT=43;
COMMIT;

/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
````

# ${\color{red} \textbf{Backend}}$

### ${\color{green} \textbf{dockerfile}}$
````
FROM ubuntu:latest

RUN apt update -y

RUN apt install openjdk-11-jdk -y 

RUN apt-get install maven -y 

RUN apt-get install git -y 
  
RUN git clone https://github.com/guru6910/Project-Angular-App.git 

WORKDIR /Project-Angular-App/spring-backend

COPY application.properties src/main/resources/application.properties

RUN mvn clean package -Dmaven.test.skip=true

EXPOSE 8080

CMD ["java", "-jar", "target/spring-backend-v1.jar"]
````

### ${\color{green} \textbf{application.properties}}$
````
spring.datasource.url=jdbc:mysql://172.17.0.2:3306/springbackend?useSSL=false
spring.datasource.username=root
spring.datasource.password=1234

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
