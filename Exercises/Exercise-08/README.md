# Exercise 08: Jenkins Job Configuration and Build

## Objective
Create a GitHub repository containing a simple shell script, configure Jenkins to retrieve the script from GitHub, and run it as a Jenkins build.

## Procedure

### 1. Confirm Jenkins is running
Check the existing Jenkins container:

```bash
docker ps -a
```

If the container exists but is stopped, start it:

```bash
docker start jenkins
```

Open Jenkins at [http://localhost:8080](http://localhost:8080).

![Jenkins container status](screenshots/01-jenkins-container-status.png)

### 2. Create the GitHub repository
Create a public repository named `devops-sample-code`. Leave README, `.gitignore`, and license initialization disabled so the repository is empty before pushing the local commit.

![Create GitHub repository](screenshots/02-create-github-repository.png)

### 3. Create the shell script
Create `hello-world.sh` with the following contents:

```bash
#!/bin/bash
echo "Hello, Jenkins!"
```

![hello-world.sh source](screenshots/03-hello-world-script.png)

Make the script executable:

```bash
chmod +x hello-world.sh
```

![Make script executable](screenshots/04-make-script-executable.png)

### 4. Initialize Git and commit the script
Configure your Git identity if it has not already been configured, then initialize the repository and stage the script:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git init
git add hello-world.sh
git status
```

![Initialize local Git repository](screenshots/05-initialize-git-repository.png)

Stage the script and check the staging area:

```bash
git add hello-world.sh
git status
```

![Stage shell script](screenshots/06-stage-script.png)

Commit the staged file:

```bash
git commit -m "Add hello-world.sh"
```

![Commit shell script](screenshots/07-commit-script.png)

### 5. Connect the local repository to GitHub
Add the repository URL as the `origin` remote. For this exercise:

```bash
git remote add origin https://github.com/yogeshgowdais23/devops-sample-code.git
git branch -M main
git push -u origin main
```

If `origin` already exists, use `git remote set-url origin <repository-url>` instead of adding it again.

![Add GitHub remote](screenshots/08-add-github-remote.png)

![Push commit to GitHub](screenshots/09-push-to-github.png)

### 6. Open Jenkins and create a job
Open [http://localhost:8080](http://localhost:8080). From the Jenkins dashboard, choose **New Item**, enter a job name such as `HelloWorld`, select **Freestyle project**, and click **OK**.

![Jenkins dashboard](screenshots/12-jenkins-dashboard.png)

### 7. Configure Source Code Management
In the job configuration:

1. Under **Source Code Management**, select **Git**.
2. Enter the repository URL: `https://github.com/yogeshgowdais23/devops-sample-code.git`.
3. For a public repository, leave credentials as **None**.
4. Set the branch specifier to `*/main`.

If Git is not available as an SCM option, install the Jenkins **Git** plugin under **Manage Jenkins → Plugins**.

### 8. Configure the build step
Under **Build Steps**, select **Execute shell** and enter:

```bash
chmod +x hello-world.sh
./hello-world.sh
```

Save the job and select **Build Now**.

### 9. Verify the build
Open the build's **Console Output**. The output should include:

```text
Hello, Jenkins!
Finished: SUCCESS
```

![Successful Jenkins build console output](screenshots/13-successful-jenkins-build.png)

## Result
A shell script was committed to GitHub, retrieved by Jenkins through Source Code Management, and executed successfully as a Jenkins build.
