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
