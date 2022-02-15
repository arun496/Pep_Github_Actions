# Github Actions
#### 1. To create your CI/CD pipeline
#### 2. To automate your software development process => Building, Testing, Deploying

### Pre-requisites
#### 1. Basics of Git [Push, Commit, Pull-request, Merge, Branches]
#### 2. YAML syntax

## Flow in Github Actions
### Terminologies to know while working with github actions
#### 1. Events
#### 2. Action
#### 3. Workflows
#### 4. Runners

### Events
#### In github, we have certain pre-defined tasks which are referred to as events. That is, push, pull request, merge all resemble events. Whenever any event occurs, we can perform certain actions to be carried out.

### Actions
#### From the above github events, we can trigger certain actions which we have pre-defined in Github Actions. We can either pick pre-defined actions from `Github Marketplace` where you can find all kinds of actions already available or we can create our own custom actions in JavaScript Lang itself.
#### Examples of actions, let's say you want to use a pre-defined template of a particular process; that is deploying to Digita Ocean or Heroku (since the process requires authentication, stuff like that, eases our work); or Sending a feedback message to the Dev Team Slack Channel whenever our CI/CD Pipeline fails or succeeds.

### Workflows
#### Workflow is nothing but a yaml file in which you write your entire process to carried out. Let's say you want to execute all of the build process in a workflow with your build commands `npm run build` for react in so-called build.yml file. Then to test you app create a another workflow file test.yml with your test commands and likewise. You can have more than one workflows and all of them will run in parallel.
#### `Note:` Your workflows are yaml files and the folder structure to place these files so that github could recognize and perform operations is mentioned below:
#### In the root folder of your project (i.e. root of your github repo), create `.github` directory and in that create `workflows` directory, then place all your .yml files i.e. your workflows in that `workflows directory`. 
#### `.github > workflows > build.yml`

### Runners
#### Runners are nothing but virtual machines (servers) hosted by github in which workflow gets executed. These runner are managed by github itself, just that the process gets executed over the virtual environment in those runners. We can also customize the virtual environment with our requirements like having OS like windows or macOS or linux and can also install our new dependencies like node and check our for those environments.
#### Also if we want customizations over hardware like some other OS then we can also execute the process over our own self-hosted runner (i.e. own server which has to be pre-installed with the Github actions software to work with.)
#### Therefore, we can have 2 types of runners
#### 1. Github hosted runners (comes with required softwares installed)
#### 2. Self-hosted runners (we have to install required softwares)

## Your first workflow
#### Breaking down further, your workflow will have 3 major properties to consider:
#### 1. Event (Already discussed let's say on push event this particular workflow will execute)
#### 2. Jobs
#### 3. Steps

### Jobs and steps
#### Basically you want to excute an entire CI/CD workflow for your project. So you will mention your individual jobs to be carried out, example build, test. And in each of those jobs you want need to mention steps to execute a job, examples you will mention your build command as a step `npm run build`, for test your step `npm test -- --coverage`. `Note:` Actually, in a workflow these jobs would be running in runner let it be windows or linux. And any job can run in any runner type you wish.

#### `.github > workflows > firstworkflow.yml`

```yml
# Use # for commenting

# Naming my workflow, it's optional to name even in jobs and steps
name: Execute simple Commands

# on is the keyword to say on this particular event, example push event
# For single event
on: push

# For multiple events, enclose in array with comma-separated
# on: [push, fork]

# All of the jobs you want to execute, theses jobs will execute parallely if they are independent jobs to execute.
# Note: Can also add "needs" as a step in a job to be dependent on another job
jobs:
  # Can have more than one jobs running
  # Name your job
  execute-linux-commands:
    # The Github hosted runner with the OS you want
    runs-on: ubuntu-latest
    # All of the steps you your actual commands which will complete your tasks
    # In these steps, you will use pre-defined Github Actions or custom actions
    steps: 
      # - hyphen indicates the array of steps you want execute one after the other
      # Naming my step
      - name: List all files & Directories
        # The command which you want to run in your terminal
        run: ls
      # The output of these steps and jobs can be found in the actions tab in your Github repository at the top.
      - name: Echo a simple string
        run: echo "Hello World!!"

  # Just executing another job parallely with another OS
  execute-windows-commands:
    runs-on: windows-latest
    steps:
      # Some common software like Git, Node, npm, curl will be pre-installed.
      - name: Check version
        # Execute multi-line commands
        run: |
          git --version
          node -v
          npm -v
```

#### Now try to make a git push in your repository which would cause push event and you would see all of your workflows being executed. Refer below image.

#### Here in the Actions tab of your repository you can find the detailed output of your workflows, jobs, steps
![Alt text](./resources/ref-1.png?raw=true "Optional Title")

## Running steps in different shells in your wokflow for each steps

```yml
name: Execute simple Commands

on: push

jobs:
  execute-windows-commands:
    runs-on: windows-latest
    steps:
      - name: Check node.js version
        run: |
          node -v
          npm -v
        shell: pwsh # powershell
      - name: Check Git version
        run: git --version
        shell: cmd # command prompt

  execute-linux-commands:
    runs-on: ubuntu-latest
    steps: 
      - name: List all files & Directories
        run: ls
      - name: Echo a simple string
        run: echo "Hello World!!"
        shell: bash # bash
```
## A Job dependent on one or more other jobs in a workflow

#### By default all Jobs in a workflow runs parallely, so use "needs" for dependency of other jobs.

```yml
name: Execute simple Commands

on: push

jobs:
  execute-linux-commands:
    runs-on: ubuntu-latest
    steps: 
      - name: Echo a simple string
        run: echo "Hello World!!"

  execute-windows-commands:
    runs-on: windows-latest
    steps:
      - name: Check node.js version
        run: node -v

  execute-mac-commands:
    runs-on: macos-latest
    # This "needs" shows an array of dependent job. i.e. Only after those jobs this job would execute.
    needs: ["execute-linux-commands", "execute-windows-commands"]
    steps:
      - name: Listing directories and files
        run: ls
```

#### `Note` [Refer this link for different shells and different OS](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#using-a-specific-shell)

## Using first Action in our workflow from the Github Marketplace
#### Here we will be using the `actions/hello-world-javascript-action`. This action which is already pre-defined just prints "Hello World" to your output. Note, we can also write our own customised actions like these since actions are written in JavaScript only by using some modules available by github.
#### `actions/hello-world-javascript-action`, here actions is the global user by github in which we have all actions repository are published and "hello-world-javascript-action" is one among them.
#### `Note` [ Refer this link to know more about this action](https://github.com/actions/hello-world-javascript-action)

```yml
name: Using First Action

on: push

jobs:
  execute-hello-world-action:
    runs-on: ubuntu-latest
    steps:
      - name: Simple Hello world action
        id: hello_world # Giving unique identity to this step to refer in future
         # "uses" defines which Action to be used
        #  "v1" indicates the version release of the action repository, can also use latest commit id of the action instead of "v1"
        uses: actions/hello-world-javascript-action@v1
        # Can assume an action to be a funtion, thereby the function can take input for the action
        # "with" is used to pass the input for keyword "who-to-greet" already in the action
        with: 
          who-to-greet: Alexander
      - name: Outputs of the action # Want to log the output being given by the function
        run: |
          echo "Hey this is your output from hello_world action",
          echo "${{ steps.hello_world.outputs.time }}"
        # So, above we have ${{}} called "Expression context" which indicates for dynamic value assigned to output (just like JS template literal string).
        # Here, to access output github has many objects pre-defined which can be used,
        # "steps" an object which have all steps detail so to target "hello_world" step we used it, by giving "id" to identify, 
        # then "outputs" is output from the referred step, then finally the variable "time" which is defined in action itself which contains the exact output
```

#### `Note` We also have other global objects just like "steps" which can be used in the workflow and those are "github", "job", "runner", "steps", etc...

![Alt text](./resources/ref-2.png?raw=true "Optional Title")
![Alt text](./resources/ref-3.png?raw=true "Optional Title")

## Using one of the most important action `Checkout Action`
#### When your workflow runs in the runner virtual machine it will not have the code files and folders of your repository. Let's say you want to build or test your code, for that you must have all your code files only then you can execute the build process. So checkout actions helps in pulling the code of your repository in that virtual machine and then you can follow any of the build or test or deploy process. This checkout action is officially created by Github itself.

```yml
name: Checkout our code

on: push

jobs:
  checkout-code-in-runner:
    runs-on: ubuntu-latest
    steps:
        # To verify whether the runner actually contains the code files and folder or not
      - name: Code check before Checkout Action
        # This "pwd" will be the default working directory in which our code wil be placed
        run: |
          pwd 
          ls
      - name: Checkout code using Checkout Action
        uses: actions/checkout@v1
      - name: Code check after Checkout Action
        run: |
          pwd 
          ls
```

#### This time we can see the files and folders being present in our repository logged from the below image, since the entire codebase is pulled
![Alt text](./resources/ref-4.png?raw=true "Optional Title")

## Trigger workflows only event occurs on particular branch
#### So let's say you have more number of branches associated with your project -> main, develop, bugfix and so on... Now I want to trigger the workflow only when the push event occurs in the particular "develop" branch and not for other branch events. So we need to describe these details in our workflow

```yml
name: Worflow for develop branch

# Now these below line nowhere defines any additional details for the event
# on: [push] 
# on: push

# Inorder to describe additional details we have to specify the events syntax in objects (key-value) pattern
on:
  push:
    branches: 
      - develop
      - main 
      # Want to trigger in "main" branch as well, then can put like this array of branches format
  # To trigger workflow in PR for the "main" branch
  pull_request:
    branches:
      - main

jobs:
  execute-linux-commands:
    runs-on: ubuntu-latest
    steps: 
      - name: Echo a simple string
        run: echo "Hello World!!"
```

#### `Note` [For more additional details and parameters to consider refer this link](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#push)

## Using Environment Variables in our worlflow
#### Different scopes of environment variables are available and can be used in our workflow.
#### 1. Workflow scope (Available across workflow and can be used anywhere in jobs, steps)
#### 2. Jobs scope (Available across the job and can be used in any steps of that workflow)
#### 3. Steps scope (Available across the step and can be used in that step)
#### 4. Global scope (These are globally available env's provided by Github itself which can be used anywhere in the workflow)

```yml
name: Environment Variables in workflow

on: push
# Env for the workflow scope - available for all jobs
env:
  MY_WORKFLOW_ENV: Env for workflow scope

jobs:
  execute-my-env:
    runs-on: ubuntu-latest
    env: 
      MY_JOBS_ENV: Env only for "execute-my-env" - Job scope
    steps: 
      - name: Echo a simple string
        id: Echoing
        env:
          MY_STEP_ENV: Env only for "echoing" step - Step scope
        # Env can be accessed in same dynamic variable type
        run: |
          echo "MY_WORKFLOW_ENV: ${MY_WORKFLOW_ENV}"
          echo "MY_JOBS_ENV: ${MY_JOBS_ENV}"
          echo "MY_STEP_ENV: ${MY_STEP_ENV}"
  
  execute-global-scope-env:
    runs-on: ubuntu-latest
    # Execute self-defined env variables along with global scope env available by github
    steps: 
      - name: Check Self-defined & Global Env's
        run: |
          echo "MY_WORKFLOW_ENV: ${MY_WORKFLOW_ENV}"
          echo "MY_JOBS_ENV: ${MY_JOBS_ENV}"
          echo "MY_STEP_ENV: ${MY_STEP_ENV}"

          echo "HOME: ${HOME}"
          echo "GITHUB_WORKSPACE: ${GITHUB_WORKSPACE}"
          echo "GITHUB_ACTOR: ${GITHUB_ACTOR}"
          echo "GITHUB_REPOSITORY: ${GITHUB_REPOSITORY}"
```

#### [Refer more on Global or default environment variables from this link](https://docs.github.com/en/actions/learn-github-actions/environment-variables#default-environment-variables)
#### Refer below image for the output of the above workflow

![Alt text](./resources/ref-5.png?raw=true "Optional Title")
![Alt text](./resources/ref-6.png?raw=true "Optional Title")

## Pass Environment variables as encrypted Secrets across your workflows
#### Now we would not want to expose our environment variables, so to encrypt and make it available (access) globally across all your workflows, jobs and steps follow below steps.
#### Go to your repository in Settings tab and then Secrets in sidebar and click actions. Now add your repository secret with the key-value pair. That's it!!

![Alt text](./resources/ref-7.png?raw=true "Optional Title")

#### Now you can access the secret added in any of your workflow files

```yml
name: Secret Environment Variables in workflow

on: push
# Accessing your repo secret as an environment variable by not exposing your value
env:
  MY_SECRET_ENV: ${{ secrets.MY_REPO_SECRET }}

jobs:
  execute-my-env:
    runs-on: ubuntu-latest
    steps: 
      - name: Echo a simple string
        run: echo "Hello World!"
```

#### Being a secret the value is not exposed and logged
![Alt text](./resources/ref-8.png?raw=true "Optional Title")

## Using If conditionals in our workflows
### So now we can use certain If conditionals in our workflow both "Jobs" level as well as at "Steps" level. Saying if so and so condition meets only then trigger the job or step.

```yml
name: Workflow - If conditionals

on: push

jobs:
  execute-linux-commands:
    runs-on: ubuntu-latest
    # Checks if the event is "push" only then this job will execute
    # If at Job level
    if: github.event_name == 'push'
    # When a step fails all other steps which have to be executed "will not" execute, since they trigger sequentially
    # So lets handle just like Exceptional handling we do in Java
    steps:
      - name: Print Hello World
        # Made a spelling mistake, so that this step fails as a result below steps will not execute
        run: eccho "Hello World"
      - name: Node.js version
        # Since above step fails, but still I want to run this step
        if: failure() # will give true if if above step fails, thereby this step will execute
        run: node -v
      - name: npm version
        run: npm -v
      - name: Git version
        run: git --version
        # Run this step always regardless of whatsoever happens in above steps - success or failure
      - name: Print Working Directory
        if: always()
        run: pwd

  execute-simple-commands:
    runs-on: ubuntu-latest
    steps: 
      - name: List all files & Directories
        run: ls
```

