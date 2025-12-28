# Jenkins

## Introduction to Jenkins

- Jenkins is an open-source automation server for Continuous Integration (CI) and Continuous Delivery (CD) practices.

- To automate the repeated tasks of building, testing, and deploying applications.

---

## Job Creation Steps

1. Select the type of **job** structure(eg: **freestyle, pipeline** etc).

2. Configure with a name for the job.

3. **Specify SCM (Source Code Management):** Specify Git and the Git repository URL.

4. **Define Build Execution:** Set an Execution shell command for the desired workflow (e.g., compile, test).

5. **Post-Build Actions:** Specify email or **slack** for access and to print success/failure notifications. Otherwise just simply specify the print statements in success/failure section itself.

---

## How to give triggers to a job

Go to **jobs** $\rightarrow$ **configure** $\rightarrow$ **triggers** $\rightarrow$ **Build periodically** $\rightarrow$ **schedule**

-   To build every minute: Type `* * * * *`
-   Every hour: Type `H * * * *`
-   Every weekdays: Type `H * * * 1 3 5` (1,3 and 5 denotes Monday, Wednesday and Thursday respectively as in days).
-   **Providing credentials** is used as a protection mechanism.
-   By providing credentials, Jenkins and GitHub understands it is a valid user and will provide the source so the build will be fast.
-   Otherwise, it may take some time to verify every build and it may be difficult in case of more codes to build.

---

## Groovy
* Used to run Jenkins.
* **Syntax and usage:**
    * **Variables:** Examples: `int g = 20;`.
    * **Control Structures:** Use `if-else`, `loops (for, while)`, and enclose the code block with `{ }`.
    * **Strings:** Use single quotes for **literals**, double quotes for **interpolation** (e.g., `"Hello, ${name}"`).

---

## Shared Library

* A **shared library** is a reusable library that can be shared by an organization or used within the project so anyone can use the library.
* **JCasC (Jenkins Configuration as Code):** Mentioned as JCasC $\rightarrow$ **Jenkins Configuration as a Code**.

#### Creating and Using a Shared Library
* **Creation:** To create a shared library, go to **Manage Jenkins** $\rightarrow$ **System** $\rightarrow$ **Global Trusted Pipeline Libraries**.
* Requires a **Name**, **Repository URL**, and **Credentials**.
* Once configured, if you create a pipeline script or any script, you can call this library.

### Shared Library Example in a Pipeline

```groovy
@Library('python-sharedLib@v-9.9')
pipeline {
    agent any
    environment {
        APP_NAME = 'python-calculator'
        PYTHON_VERSION = '3.9'
    }
    stages {
        stage ('install dependencies') {
            steps {
                // Calls a function defined in the shared library repository
                install_dependencies() 
            }
        }
    }
}

```

* The function `install_dependencies()` is defined in the shared library repo and is executed within the install dependencies stage by calling it from the pipeline script.