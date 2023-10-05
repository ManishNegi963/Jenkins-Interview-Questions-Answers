# Jenkins Interview Questions & Answers

- **Difference between CI, Continuous Delivery and Contnious Deployment?**

    - Continuous Integration: The process of automatically integrating the code changes into shared repository (Git), building the image from the code (Docker) , testing (sonarqube) is known as continuous integration.
 
    - Continuous Delivery: After continuous integration, the process of automatically deilvering tested code to pre-production environment and allowing manual intervention before production deployment.
 
    - Continuous Deployment: After continous integration, the process of automatically deploying the code to the production without any manual intervention is called contiuous deployment.
 
 - **On Which port Jenkins run**

    - Jenkins run on port 8080, so add rule 8080 to securoty group of ec2.
  
     <img width="874" alt="image" src="https://github.com/ManishNegi963/Jenkins-Interview-Questions-Answers/assets/124788172/f97faa5a-a652-4c80-9e56-be24a82dec84">

- **Syntax of Declarative pipeline?**

              pipeline{
                  agent any
                        
                      stages{
                          stage("Code cloned"){
                                steps {
                                    echo "Cloning the code"
                                    git url: https://github.com/ManishNegi963/django-notes-app.git , branch: "main"
                          }
                      }
                          
                          stage("Code Build"){
                                steps {
                                    echo "Builind the code"
                                    sh "docker build . -t notes-app
                                   
                          }
                      }
                           stage("Code Pushed"){
                                steps {
                                    echo "Pushing the code to DockerHUb"
                                    withCredentials([usernamePassword(credentialsId:"DockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                                    sh "dpcker tag notes-app ${env.dockerHubUser}/notes-app:latest        
                                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}
                                    sh "docker push ${env.dockerHubUser}/notes-app:latest"
                              }
                                   
                          }
                      }
                           stage("Code Deployed"){
                                steps {
                                    echo "Deploying the code"
                                    sh "docker-compose down && docker-compose up -d"
                                    
                          }
                      }
                          
                  }
              }

    stages refer to different stage of continuus integration and deployment
    stages are the high level phases of the pipeline and steps are the individual task within each stage.
    IN stage "Code Pushed", we used username and password through environment variables -GO to manage jenkins-> credentials -> system->global and add credentials of DockerHUb.
    Used "DockerHub" as name of credentialsId
    agent refers to the server defined where the job is to done.


- **Purpose of agent**

     - In Jenkins, an agent, also known as a Jenkins agent or Jenkins slave, plays a crucial role in the distributed architecture of Jenkins. The primary purpose of an agent is to offload the execution of jobs or tasks from the Jenkins master server to remote machines. This distributed approach allows Jenkins to scale and handle a larger number of builds and tasks efficiently.
 
     - To set up an agent in Jenkins, you typically need to install the Jenkins agent software on the target machine and then configure the agent to connect to the Jenkins master.
     - It defines the node or machine where pipeline steps will be executed.
 
- **How to build a docker image in the declarative pipeline?**

                   pipeline{
                          agent any
                                
                              stages{
                                  stage("Code cloned"){
                                        steps {
                                            echo "Cloning the code"
                                            git url: https://github.com/ManishNegi963/django-notes-app.git , branch: "main"
                                  }
                              }
                                  
                                  stage("Code Build"){
                                        steps {
                                            echo "Building the code"
                                            sh "docker build . -t notes-app
                                           
                                  }
                              }
                }
            }

  - **HOw do you handle parallel execution of stages in Jenkins declaratuve pipeline?**
 
       - Using "parallel" directive within stage block, to define mulitple stages that can be executed concurrently.
   
       - In Jenkins Declarative Pipeline syntax, you can use the `parallel` directive to execute multiple stages concurrently. This is useful when you have independent tasks or tests that can be performed simultaneously, thereby reducing the overall pipeline execution time. Here's an example of how you can achieve parallel execution of stages in a Jenkins Declarative Pipeline:

                    ```groovy
                    pipeline {
                        agent any
                    
                        stages {
                            stage('Parallel Stage') {
                                parallel {
                                    stage('Stage A') {
                                        steps {
                                            echo 'Running Stage A'
                                            // Add your commands or build steps for Stage A
                                        }
                                    }
                    
                                    stage('Stage B') {
                                        steps {
                                            echo 'Running Stage B'
                                            // Add your commands or build steps for Stage B
                                        }
                                    }
                    
                                    stage('Stage C') {
                                        steps {
                                            echo 'Running Stage C'
                                            // Add your commands or build steps for Stage C
                                        }
                                    }
                                }
                            }
                    
                            stage('Final Stage') {
                                steps {
                                    echo 'Running Final Stage'
                                    // Add your commands or build steps for the final stage
                                }
                            }
                        }
                    }
                    ```

    In this example:
    
    1. The `parallel` directive is used inside the 'Parallel Stage' to define parallel branches for Stage A, Stage B, and Stage C.
    2. Each parallel branch represents a separate stage with its own set of commands or build steps.
    3. The stages inside the `parallel` block will be executed concurrently.
    4. The 'Final Stage' will only be executed once all the parallel branches (Stage A, Stage B, Stage C) are completed.
   
-**What are some common methods for handling error and exception in Jenkins pipeline?**

   - catchError

-**How to schedule a pipeline execution at particular time?**

  - By using build periodically
