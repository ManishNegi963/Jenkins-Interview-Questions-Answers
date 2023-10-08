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

In Jenkins, the `catchError` step is part of the Declarative Pipeline syntax and is used to catch errors that occur within a block of code and handle them gracefully. It allows you to define a set of steps to be executed when an error occurs, providing a way to perform cleanup or take specific actions in response to the error.

Here's a basic example of how you might use `catchError` in a Jenkins Declarative Pipeline:

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                catchError(buildResult: 'FAILURE') {
                    script {
                        // Your build steps here
                        sh 'make all'
                    }
                }
            }
        }
    }

    post {
        failure {
            // Steps to execute when an error occurs
            echo 'Build failed! Taking corrective actions...'
            // Additional steps or notifications can be added here
        }
    }
}
```

In this example:

- The `catchError` block wraps the build steps, and it specifies that it should catch errors when the build result is 'FAILURE'.
- If any of the steps inside the `catchError` block fails, the pipeline will not be immediately aborted. Instead, the steps inside the `failure` block under the `post` section will be executed.
- In the `failure` block, you can define additional steps or notifications to be executed in response to the failure.

You can customize the `catchError` block based on your specific requirements. For example, you can catch errors for specific stages or steps and define different actions for different types of failures.

Keep in mind that `catchError` is specific to Declarative Pipelines in Jenkins and may not be available or necessary for Scripted Pipelines. If you are using Scripted Pipeline syntax, you might handle errors using try-catch blocks in Groovy scripts.
     

-**How to schedule a pipeline execution at particular time?**

  - By using build periodically

In Jenkins, you can schedule pipeline executions at a particular time using the "Build periodically" option in the pipeline configuration. Here's how you can do it:

1. **Open your Jenkins Pipeline Job:**
   - Navigate to the Jenkins dashboard.
   - Click on the name of your pipeline job.

2. **Configure the Pipeline:**
   - Click on "Configure" on the left sidebar to edit the pipeline configuration.

3. **Build Triggers:**
   - Look for the "Build Triggers" section in the configuration page.

4. **Schedule the Pipeline Execution:**
   - Check the option "Build periodically."
   - In the "Schedule" field, enter the cron syntax to specify when the pipeline should run. The cron syntax consists of five fields (minute, hour, day of month, month, day of week), separated by spaces.
     - For example, to schedule the pipeline to run every day at 2 AM, you would enter `0 2 * * *`.

   Here are some examples of cron expressions:

   - **Every day at 2 AM:**
     ```
     0 2 * * *
     ```

   - **Every weekday at 8 AM:**
     ```
     0 8 * * 1-5
     ```

   - **Every Monday and Thursday at 3:30 PM:**
     ```
     30 15 * * 1,4
     ```

   - **Every Sunday at midnight:**
     ```
     0 0 * * 0
     ```

   - You can use online cron expression generators to help you create the expression you need.

5. **Save the Configuration:**
   - After configuring the schedule, scroll down and click on the "Save" button to save the changes.

Now, your Jenkins pipeline will be scheduled to run at the specified times based on the cron expression you provided. Jenkins will automatically trigger the pipeline executions according to the schedule you defined.


-**What is a choice parameter?**

Certainly! Let's create a simple example of a Jenkins pipeline with a Choice Parameter. In this example, we'll create a pipeline that deploys an application to different environments based on the user's choice.

1. **Create a new Jenkins Pipeline:**
   - Go to your Jenkins dashboard.
   - Click on "New Item" to create a new pipeline.
   - Enter a name for your pipeline (e.g., "DeployAppPipeline") and choose "Pipeline" as the type.
   - Click "OK" to create the pipeline.

2. **Configure the Pipeline:**
   - In the pipeline configuration, scroll down to the "Pipeline" section.
   - Choose "Pipeline script" as the definition.
   - Here's a basic pipeline script with a Choice Parameter:

   ```groovy
   pipeline {
       agent any

       parameters {
           choice(name: 'ENVIRONMENT', choices: ['Development', 'Staging', 'Production'], description: 'Select deployment environment')
       }

       stages {
           stage('Checkout') {
               steps {
                   // Your checkout steps here
                   echo "Checking out code..."
               }
           }

           stage('Deploy') {
               steps {
                   script {
                       echo "Deploying to ${params.ENVIRONMENT} environment..."
                       // Add deployment steps based on the selected environment
                   }
               }
           }
       }
   }
   ```

3. **Save and Run the Pipeline:**
   - Save the pipeline configuration.
   - Click on "Build Now" or trigger the pipeline manually.

4. **Parameter Input:**
   - When the pipeline is triggered, it will prompt the user to choose an environment from the provided choices.

   ![Jenkins Choice Parameter](./images/jenkins_choice_parameter.png)

5. **View Console Output:**
   - The pipeline will run, and you can view the console output to see the selected environment and the deployment steps executed.

This example demonstrates a simple pipeline with a Choice Parameter named `ENVIRONMENT`. The user can choose between 'Development,' 'Staging,' or 'Production' when triggering the pipeline. The pipeline script uses the selected environment to customize the deployment steps accordingly.

You can expand on this example by adding more stages, integrating with version control systems, or incorporating more complex deployment logic based on the chosen environment.

-**HOw to register agent node to master?**

 Now go to the master node
 
step 1: cd .ssh/

step 2: ssh-keygen ed25519 press enter and name it jenkins_keys and enter, it will generate jenkins_keys private key and jenkins_keys.pub public keys

Step 3: Copy the public key from jenkins_keys.pub and paste it into the agent node authorized_keys

cat jenkins_keys.pub-> authorized_keys

Step 4: Copy the private keys in jenkins_keys and paste them into the node_todo_cicd project on the Jenkins website.

Go to Manage Credentials-> add credentials-> paste private keys of jenkins_keys->now go to configure node-todo-cicd and replace it with new credentials and build now.

-**I want to create a pipeline in Jenkins which need to have 10 different stages and based on my input it needs to execute some specific stage not every stage? How to configure this?**

To create a Jenkins pipeline with 10 different stages where you can choose to execute specific stages based on your input, you can use a combination of the `input` step and conditional logic in the pipeline script. Here's an example:

```groovy
pipeline {
    agent any

    parameters {
        choice(name: 'SELECTED_STAGE', choices: ['Stage1', 'Stage2', 'Stage3', 'Stage4', 'Stage5', 'Stage6', 'Stage7', 'Stage8', 'Stage9', 'Stage10'], description: 'Select a stage to execute')
    }

    stages {
        stage('Stage1') {
            when {
                expression { params.SELECTED_STAGE == 'Stage1' }
            }
            steps {
                echo "Executing Stage1"
                // Add your Stage1 steps here
            }
        }

        stage('Stage2') {
            when {
                expression { params.SELECTED_STAGE == 'Stage2' }
            }
            steps {
                echo "Executing Stage2"
                // Add your Stage2 steps here
            }
        }

        // Repeat similar blocks for Stage3 to Stage10

        // ...

        stage('Final Stage') {
            steps {
                echo "Executing Final Stage"
                // Add your Final Stage steps here
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully"
        }
        failure {
            echo "Pipeline execution failed"
        }
    }
}
```

In this example:

- The `parameters` block includes a `choice` parameter named `SELECTED_STAGE` with options for each stage.
- Each stage in the `stages` block is wrapped in a `when` block that checks if the selected stage matches the current stage. If there's a match, the stage will be executed; otherwise, it will be skipped.
- The `post` block includes success and failure conditions to provide a message based on the overall success or failure of the pipeline.

When you run this pipeline, Jenkins will prompt you to select a stage to execute based on the choices provided in the `SELECTED_STAGE` parameter. The selected stage and subsequent stages will then be executed, and the pipeline will provide feedback based on the success or failure of the execution.

Adjust the pipeline script according to your specific stages and requirements.


