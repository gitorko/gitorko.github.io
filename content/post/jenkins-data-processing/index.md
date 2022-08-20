---
title: 'Jenkins - Data Processing'
description: 'Jenkins - Data Processing'
summary: 'Using jenkins as a data processing pipeline'
date: '2021-11-07'
aliases: [/jenkins-data-processing/]
author: 'Arjun Surendra'
categories: [ETL, Jenkins]
tags: [pipeline, ETL, jenkins]
toc: true
---

Jenkins is mostly used to setup CI/CD pipelines. Here we will use it to setup a data pipeline that can be used to orchestrate data processing jobs. 

Github: [https://github.com/gitorko/project84](https://github.com/gitorko/project84)

## Requirement

Lets consider a company sells paint. 

* <b>STAGE1:</b> They get their orders from the field in file format to their FTP server. This has to be processed and uploaded to the db. As these are large files ability re-run jobs after fixing files is required.
* <b>STAGE2:</b> They receive their material supplier in file format to their FTP server. This has to be processed and uploaded to the db. As these are large files ability re-run jobs after fixing files is required.
* <b>STAGE3:</b> Once the order and material is uploaded to db, if the order can be fulfilled the paint color and quantity need to be grouped by city.
* <b>STAGE4:</b> Additional buffer needs to be added to cover any shortages. This job is a small job and can be run in parallel.
* <b>STAGE5:</b> Sales rep bonus point need to be added in case the offer is active. This job is a small job and can be run in parallel.
* <b>STAGE6:</b> Order needs to be sent to factory in each city.

![](ETL.png)

The features of jenkins that make it friendly for data processing are:

1. Log view - Ability to look at logs across different stages
2. Graph view - Ability to look at a run graphically.
3. Input - Ability to provide input to job at run time.
4. Scheduling - Ability to schedule jobs at periodic interval.
5. Parallel execution - Ability to run jobs in parallel
6. Agents load distribution - Ability to run the job on other agent machines distributing the load.
7. Time to complete - Ability to see which job is running and history of runs.
8. Time taken - Ability to view each stage time taken over long periods to identify trends in execution.
9. Re-Run - Ability to re-run a particular stage of the failed job.
10. Slack - Ability to notify users on slack after job completion.
11. Pull from maven - Ability to download the jar from maven.
12. Plugin support - Numerous plugin are available for jenkins.

## Code

The backend job that needs to do the processing. It takes the input as arguments and processes each stage and writes the results to a postgres db.

1. Ensure that each job can run in isolation and updates just one table. 2 stages should never update the same table.
2. Ensure that it throws runtime exception in case of failure.
3. Ensure logging is correctly added to identify the issue.
4. Ensure the stage can be re-run many times. This is done by resetting the data.
5. Change the value of BASE_PATH accordingly.

{{< ghcode "https://raw.githubusercontent.com/gitorko/project84/main//src/main/java/com/demo/project84/Main.java" >}}

The properties file

{{< ghcode "https://raw.githubusercontent.com/gitorko/project84/main//src/main/resources/application.yaml" >}}

## Jenkins

![](jenkins.png)

To setup jenkins download the jenkins.war from [https://www.jenkins.io/download/](https://www.jenkins.io/download/) and start the server.
Once the server starts you will see the admin password in the console log. This will be used to setup jenkins for the first time. This will be a one time activity.

```bash
java -jar jenkins.war
```

Open the below url

[http://localhost:8080/](http://localhost:8080/)

Alternate way to setup jenkins via docker

```bash
docker run --name my-jenkins -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts-jdk11
```

Follow the steps to finish the configuration

![](img01.png)
![](img02.png)
![](img03.png)
![](img04.png)
![](img05.png)
![](img06.png)
![](img08.png)
![](img07.png)

Install the 'Pipeline Implementation for Blue Ocean plugin' to look at graphs

![](img09.png)


```bash
user: admin
pwd: admin@123
```

Go to Dashboard and click on 'New Item' and create a pipeline, enter the script below and click on 'Build Now' and ensure it is successful.

![](img11.png)
![](img12.png)
![](img13.png)
![](img14.png)

```groovy
pipeline {
    agent any

    stages {
        stage('STAGE1') {
            steps {
                echo 'STAGE1..'
            }
        }
        stage('STAGE2') {
            steps {
                echo 'STAGE2..'
            }
        }
    }
}
```

If the setup is correct this test job should be successful.

Now create 6 pipeline jobs and a master pipeline job. All stage jobs will be same as below but input param will change.

stage1-job - STAGE1
stage2-job - STAGE2
stage3-job - STAGE3
stage4-job - STAGE4
stage5-job - STAGE5
stage6-job - STAGE6

![](img15.png)

Change param accordingly
```groovy
pipeline {
    agent any

    stages {
        stage('STAGE1') {
                steps {
                    dir ("/Users/asurendra/code/pet/project84/build/libs") {
                        sh "java -jar project84-1.0.0.jar STAGE1"
                    }
                }
            }
        }
}
```

data-job-pipeline job

```groovy
pipeline {
    agent any
     parameters {
        booleanParam(name: "BONUS_OFFER", defaultValue: true)
    }
    stages {
        stage('STAGE1') {
            steps {
                build job: 'stage1-job' 
            }
        }
        stage('STAGE2') {
            steps {
                build job: 'stage2-job' 
            }
        }
        stage('STAGE3') {
            steps {
                build job: 'stage3-job' 
            }
        }
        stage("FORK") {
            parallel {
                stage('STAGE4') {
                    steps {
                        build job: 'stage4-job' 
                    }
                }
                stage('STAGE5') {
                    //If bonus points are counted for sales then run this job.
                    when { expression { params.BONUS_OFFER } }
                    steps {
                        build job: 'stage5-job' 
                    }
                }
            }
        }
        stage('STAGE6') {
            steps {
                build job: 'stage6-job' 
            }
        }

    }
}
```

Click on 'Build with Parameters' and select the input checkbox. If bonus offer is applicable STAGE5 is executed else it wont be executed.

![](img16.png)

Monitor the job

![](img17.png)

Once the job is complete click on 'Pipeline graph' this shows the path taken graphically. You can also see the time take for each stage to complete. This can be useful to monitor the job over long time periods.

![](img18.png)
![](img19.png)
![](img20.png)

Rerun the job without the bonus offer checkbox, once completed you will see the graph shows the node with STAGE5 as skipped.

![](img21.png)

Look at the 'Console Output' that track each jobs log output. You can drill down to each stage job and look at the log specific to that. 

Now lets make a stage fail and then fix the issue and re-run the stage.

Modify the material-file.txt and reduce the quantity to 10. Run the 'data-job-pipeline' job.

![](img22.png)

Since the materials are less and order cant be fulfilled the pipeline will fail, you can now look at the logs and identify the issue.

![](img23.png)

Fix the file again by changing the value back to what it was. Click on 'Restart from Stage' and select STAGE2. We need to seed the material file again hence restarting at STAGE2.

![](img24.png)

Once the job is successful you will notice that it didnt run the STAGE1 job and only ran STAGE2 and onwards.

![](img25.png)

You can even schedule this job to run daily.

## Setup

{{< markcode "https://raw.githubusercontent.com/gitorko/project84/main/README.md" >}}

## References

[https://github.com/jenkinsci/docker](https://github.com/jenkinsci/docker)

[https://www.jenkins.io/doc/book/pipeline/](https://www.jenkins.io/doc/book/pipeline/)
