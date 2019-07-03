# Running Information Analysis Service

It is a web service to populate running information.

The structure of running information is as follows.
```java
public class RunningInformation {   
    public enum HealthWarningLevel {
        LOW, NORMAL, HIGH;
    }

    private Long id;
    private final UserInfo userInfo;
    private String runningId;
    private double longitude;
    private double latitude;
    private double runningDistance;
    private double totalRunningTime;
    private int heartRate;
    private HealthWarningLevel healthWarningLevel;
    private Date timestamp;
}
public class UserInfo {
    private int userId;
    private String name;
    private String address;
}

public class UserInfo {
    private String username;
    private String address;
    private UserInfo() { }
}
```
## Deployment

### What you need

Java(jdk 1.8), Maven, Docker

### Steps

##### 0. Preparations. (Ubuntu)

##### &emsp; Install Maven
```bash
curl https://raw.githubusercontent.com/hackjutsu/workstation-configurations/master/docker_install.sh -o docker_install.sh
chmod a+x ./docker_install.sh
```
##### &emsp; Install Docker
```bash
# step 1: update ubuntu
sudo apt-get update
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# step 2: Install GPG certificate
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# step 3: Write down software source information
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# step 4: Update and install Docker-CE
sudo apt-get -y update
sudo apt-get -y install docker-ce
```
##### 1. Choose the database. 

##### &emsp; h2database
&emsp; Launch docker
```bash
docker-compose up -d
```
##### &emsp; MySQL 
&emsp; (1) Install MySQL client(Linux)
```bash
sudo apt-get install mysql-client
```
&emsp; (2) Launch MySQL docker
```bash
docker-compose up -d
```
&emsp; (3) Login from command line
```bash
mysql --host=127.0.0.1 --port=3306 --user=root --password=root
```
&emsp; (4) Modify the dependencies in pom.xml, remove the h2database part and uncomment the MySQL part. Set the port, username and password in src.resources.application.yml. Default => user: root, password: root.

##### 2. Run
```bash
mvn clean install
```
by bash in the project folder to compile.

##### 3. Run
```bash
cd /../running-information-analysis-service/target
java -jar [jar-file-compiled by Maven]
```
Note that the default place for the jar file is in the "./target" folder.

## API

All API are implemented in RESTful style. All data operation can be achieved by http request.

### Insert

Send POST request to http://localhost:9000/. 
For example, in Postman use "localhost:9000/create" to create, the request data body are as follows.
```json
[
  {
    "runningId": "fb0b4725-ac25-4812-b425-d43a18c958bb",
    "latitude": "38.5783821",
    "longitude": "-77.3242436",
    "runningDistance": "231",
    "totalRunningTime": "123",
    "heartRate": 0,
    "timestamp": "2017-04-01T18:50:35Z",
    "userInfo": {
      "username": "ross4",
      "address": "504 CS Street, Mountain View, CA 88888"
    }
  },
  {
    "runningId": "35be446c-9ed1-4e3c-a400-ee59bd0b6872",
    "latitude": "42.375786",
    "longitude": "-76.870872",
    "runningDistance": "0",
    "totalRunningTime": "0",
    "heartRate": 0,
    "timestamp": "2017-04-01T18:50:35Z",
    "userInfo": {
      "username": "ross5",
      "address": "504 CS Street, Mountain View, CA 88888"
    }
  }
]
```

### Delete

If you want to delete all data, send delete request to http://localhost:9000

If you want to delete data with specified ID, send delete request to http://localhost:9000/{ID_to_delete}

### Modify

It rare so I didn't implement it. You can achieve it by deleting and adding a new one.

### Query

Query can be done by sending GET request to http://localhost:9000/heartRate/{heartRate}

This is aimed to get the data with heart rate equal to one nubmer, the test response({heartRate} is 113) are as follows.

```json
{
    "content": [
        {
            "runningId": "35be446c-9ed1-4e3c-a400-ee59bd0b6872",
            "latitude": 42.375786,
            "longitude": -76.870872,
            "runningDistance": 0,
            "totalRunningTime": 0,
            "heartRate": 113,
            "timestamp": 1562119617720,
            "userInfo": {
                "username": "ross5",
                "address": "504 CS Street, Mountain View, CA 88888"
            },
            "id": 6,
            "healthWarningLevel": "NORMAL",
            "username": "ross5",
            "address": "504 CS Street, Mountain View, CA 88888"
        }
    ],
    "last": true,
    "totalElements": 1,
    "totalPages": 1,
    "numberOfElements": 1,
    "sort": null,
    "first": true,
    "size": 30,
    "number": 0
}
```

Query can be done by sending GET request to http://localhost:9000/heartRateGreaterThan/{heartRate}

This is aimed to get the data with heart rate greater than one number, the test response({heartRate} is 70) are as follows.
```json
[
    {
        "userAddress": "504 CS Street, Mountain View, CA 88888",
        "heartRate": 83,
        "runningId": "7c08973d-bed4-4cbd-9c28-9282a02a6032",
        "totalRunningTime": 2139.25,
        "userName": "ross0",
        "userId": 1
    },
    {
        "userAddress": "504 CS Street, Mountain View, CA 88888",
        "heartRate": 79,
        "runningId": "07e8db69-99f2-4fe2-b65a-52fbbdf8c32c",
        "totalRunningTime": 3011.23,
        "userName": "ross1",
        "userId": 2
    },
    {
        "userAddress": "504 CS Street, Mountain View, CA 88888",
        "heartRate": 165,
        "runningId": "2f3c321b-d239-43d6-8fe0-c035ecdff232",
        "totalRunningTime": 85431.23,
        "userName": "ross2",
        "userId": 3
    },
    {
        "userAddress": "504 CS Street, Mountain View, CA 88888",
        "heartRate": 145,
        "runningId": "fb0b4725-ac25-4812-b425-d43a18c958bb",
        "totalRunningTime": 123,
        "userName": "ross4",
        "userId": 5
    },
    {
        "userAddress": "504 CS Street, Mountain View, CA 88888",
        "heartRate": 113,
        "runningId": "35be446c-9ed1-4e3c-a400-ee59bd0b6872",
        "totalRunningTime": 0,
        "userName": "ross5",
        "userId": 6
    },
    {
        "userAddress": "504 CS Street, Mountain View, CA 88888",
        "heartRate": 143,
        "runningId": "15dfe2b9-e097-4899-bcb2-e0e8e72416ad",
        "totalRunningTime": 0.1,
        "userName": "ross6",
        "userId": 7
    }
]
```
