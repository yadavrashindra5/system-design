# GitHub Actions — Complete Beginner Notes

## 1. What Is GitHub Actions?

GitHub Actions is a feature of GitHub that helps us **automatically perform tasks when something happens in our repository**.

For example, whenever we push code to GitHub, we might want to:

- Compile our application.
- Run tests.
- Check code quality.
- Build a Docker image.
- Deploy our application to a server.

Instead of doing these tasks manually every time, GitHub Actions can perform them automatically.

### Simple Example

Suppose we are developing a Java Spring Boot application.

When we push code to GitHub:

```text
Developer writes code
        |
        v
    git push
        |
        v
 GitHub Repository
        |
        v
 GitHub Actions starts
        |
        v
   Download code
        |
        v
   Set up Java
        |
        v
   Run unit tests
        |
        v
   Build application
        |
        v
  Report the result
```

If the tests fail, GitHub Actions can report the failure so we can fix the problem before merging or deploying the code.

---

## 2. Why Do We Need GitHub Actions?

Imagine that 10 developers are working on the same application.

Every time someone changes the code, we want to make sure that the application still works.

Without automation, developers may need to run tests and builds manually.

With GitHub Actions, we can automate these tasks.

### Main Benefits

1. **Automation:** Repetitive tasks run automatically.
2. **Early error detection:** Find build and test failures quickly.
3. **Consistency:** The same checks run according to the same workflow.
4. **Time saving:** Developers spend less time doing repetitive work.
5. **CI/CD:** Automate testing, building, and deployment.
6. **Integration:** Connect repository events with deployment and other development tools.

### What Is CI/CD?

**CI — Continuous Integration**

Whenever code is pushed or a pull request is opened, automatically build the application and run tests.

**CD — Continuous Delivery or Continuous Deployment**

Automate the process of preparing or releasing the application.

The distinction is important:

- Continuous Delivery keeps the application ready for release, often with a manual approval step.
- Continuous Deployment automatically releases changes when the required checks pass.

GitHub Actions can support both approaches.

---

## 3. How to Create GitHub Actions

You do not need to install GitHub Actions on your computer. It is provided by GitHub.

### Step 1: Create a GitHub repository

Create a repository on GitHub or use an existing repository.

### Step 2: Create the workflow directory

Inside your project, create this folder structure:

```text
my-project/
├── src/
├── pom.xml
├── README.md
└── .github/
    └── workflows/
        └── ci.yml
```

The important directory is:

```text
.github/workflows/
```

GitHub looks for workflow YAML files in this directory.

### Step 3: Create a YAML file

Create a file named:

```text
.github/workflows/ci.yml
```

You can name the file something else, such as `build.yml` or `deploy.yml`.

### Step 4: Write the workflow

Add the workflow configuration to the YAML file.

### Step 5: Push it to GitHub

```bash
git add .
git commit -m "Add GitHub Actions workflow"
git push origin main
```

If the workflow is configured to run on a push to `main`, GitHub will trigger it when that event occurs.

### Step 6: Check the result

Open your GitHub repository and select the **Actions** tab.

There you can inspect workflow runs, logs, and success or failure status.

---

## 4. Important GitHub Actions Terminology

Before learning the keywords, understand these concepts.

| Term | Simple meaning |
|---|---|
| Workflow | The complete automation configuration. |
| Event | Something that triggers a workflow. |
| Job | A group of tasks that runs on a runner. |
| Runner | The machine that executes a job. |
| Step | One operation inside a job. |
| Action | A reusable unit of automation. |
| YAML | The configuration format used to write workflows. |
| Artifact | A file produced by a workflow that can be saved and downloaded. |
| Secret | Sensitive information, such as a deployment token. |

### Understand the hierarchy

```text
Workflow
   |
   +-- Event: When should it run?
   |
   +-- Job: What work should be performed?
          |
          +-- Runner: Where will it run?
          |
          +-- Steps: What operations should happen?
                 |
                 +-- Action (uses)
                 |
                 +-- Shell command (run)
```

Remember:

- A workflow can contain multiple jobs.
- A job contains multiple steps.
- Jobs run in parallel by default unless dependencies or other restrictions determine their order.
- Steps within a job execute sequentially by default.

---

# 5. GitHub Actions Keywords

## 5.1 `name`

Defines the name of the workflow or a job.

Example:

```yaml
name: Java CI Pipeline
```

You can see the workflow name in the GitHub Actions interface.

A job can also have a display name:

```yaml
jobs:
  build:
    name: Build Java Application
```

Here, `build` is the job ID, while `Build Java Application` is its display name.

## 5.2 `on`

Defines the events that trigger the workflow.

Example:

```yaml
on:
  push:
```

This triggers the workflow when a push event occurs on any branch, subject to GitHub's event and path filtering rules.

You can restrict the trigger to a specific branch:

```yaml
on:
  push:
    branches:
      - main
```

Now it triggers for pushes to `main`.

### Common events

**Push**

```yaml
on:
  push:
```

Runs when code is pushed.

**Pull request**

```yaml
on:
  pull_request:
    branches:
      - main
```

Runs for relevant pull request events targeting `main`.

**Manual execution**

```yaml
on:
  workflow_dispatch:
```

Allows you to start the workflow manually from GitHub, provided it is available and enabled.

**Scheduled execution**

```yaml
on:
  schedule:
    - cron: '0 2 * * *'
```

Schedules the workflow daily at 02:00 UTC. Scheduled workflows use UTC and may run later than the exact scheduled time.

These are examples of events supported under `on`.

## 5.3 `jobs`

Defines the jobs in the workflow.

Example:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Building application"

  test:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Running tests"
```

Here:

- `jobs` is the collection of jobs.
- `build` is a job ID.
- `test` is another job ID.

By default, the two jobs can run in parallel.

## 5.4 `runs-on`

Specifies which runner executes a job.

Example:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

GitHub provides a Linux runner for this job.

Common GitHub-hosted runner labels include:

```yaml
runs-on: ubuntu-latest
```

```yaml
runs-on: windows-latest
```

```yaml
runs-on: macos-latest
```

Choose a runner based on your project's operating-system and tooling requirements.

## 5.5 `steps`

Defines the operations performed inside a job.

Example:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Print a message
        run: echo "Hello GitHub Actions"

      - name: Print another message
        run: echo "Building application"
```

The steps execute sequentially by default.

The `name` inside each step is optional, but it makes logs easier to understand.

## 5.6 `uses`

Uses an existing action.

Example:

```yaml
steps:
  - name: Checkout code
    uses: actions/checkout@v4
```

This action checks out the repository's code so the runner can work with it.

Another example:

```yaml
- name: Set up Java
  uses: actions/setup-java@v4
  with:
    distribution: temurin
    java-version: '21'
```

This uses a reusable action to configure Java.

The `@v4` suffix specifies the action version reference.

## 5.7 `run`

Executes a shell command on the runner.

Example:

```yaml
steps:
  - name: Run Maven tests
    run: mvn test
```

Another example:

```yaml
- name: Print message
  run: echo "Hello World"
```

You can also execute multiple shell commands:

```yaml
- name: Build application
  run: |
    java -version
    mvn --version
    mvn package
```

The pipe (`|`) allows multiple lines in a YAML string.

Remember:

- `uses` runs an action.
- `run` executes a shell command.

## 5.8 `with`

Passes input parameters to an action.

Example:

```yaml
- name: Set up Java
  uses: actions/setup-java@v4
  with:
    distribution: temurin
    java-version: '21'
```

Here, `with` configures the action to use Java 21.

The inputs available depend on the action being used.

## 5.9 `env`

Defines environment variables.

Example:

```yaml
env:
  APP_ENV: test

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Print environment
        run: echo "$APP_ENV"
```

Environment variables can be defined at workflow, job, or step level.

A variable defined at a narrower level can override a variable with the same name at a broader level.

Example at step level:

```yaml
- name: Run tests
  env:
    APP_ENV: test
  run: mvn test
```

Do not use ordinary environment variables as a substitute for securely storing credentials.

## 5.10 `needs`

Defines a dependency between jobs.

Example:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Build application"

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploy application"
```

The `deploy` job waits for `build` to finish successfully.

```text
Build
  |
  v
Deploy
```

You can depend on multiple jobs:

```yaml
needs: [build, test]
```

The dependent job normally runs only when all required jobs succeed, unless you configure different conditions.

## 5.11 `if`

Executes a job or step only when a condition is satisfied.

Example:

```yaml
- name: Deploy application
  if: github.ref == 'refs/heads/main'
  run: echo "Deploying application"
```

The step runs only when the workflow reference is `main`.

Another common example:

```yaml
- name: Print failure message
  if: failure()
  run: echo "A previous step failed"
```

The `if` key supports expressions and status-check functions.

## 5.12 `permissions`

Controls what the workflow's `GITHUB_TOKEN` can access.

Example:

```yaml
permissions:
  contents: read
```

This grants read access to repository contents through the token.

For example, if a workflow needs to publish a release, it might need additional permissions. Grant only what is necessary.

## 5.13 `secrets`

Provides access to securely stored sensitive values.

Example:

```yaml
- name: Deploy application
  env:
    DEPLOY_TOKEN: ${{ secrets.DEPLOY_TOKEN }}
  run: ./deploy.sh
```

Before using this example, create a secret named `DEPLOY_TOKEN` in your repository or GitHub environment settings.

Never hardcode credentials directly into YAML files.

Be careful when printing secrets or passing them to third-party tools. Secret masking is not a replacement for secure handling.

## 5.14 `strategy` and `matrix`

Allows a job to run with multiple configurations.

Example:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        java: ['17', '21']

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: ${{ matrix.java }}

      - run: mvn test
```

This creates two test-job variations:

```text
Test with Java 17
       +
Test with Java 21
```

Matrix builds are useful for testing compatibility across multiple Java or Node.js versions.

## 5.15 `outputs`

Makes a value produced by a step or job available to other steps or jobs.

A job output example:

```yaml
jobs:
  prepare:
    runs-on: ubuntu-latest
    outputs:
      app_version: ${{ steps.version.outputs.app_version }}

    steps:
      - id: version
        run: echo "app_version=1.0.0" >> "$GITHUB_OUTPUT"

  build:
    needs: prepare
    runs-on: ubuntu-latest
    steps:
      - run: echo "Version is ${{ needs.prepare.outputs.app_version }}"
```

The `prepare` job exposes a value, and the dependent `build` job reads it.

## 5.16 `timeout-minutes`

Sets a maximum time for a job or step.

Example:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - run: mvn test
```

The job is cancelled if it exceeds its configured timeout.

## 5.17 `continue-on-error`

Allows the workflow to continue even if a particular step fails.

Example:

```yaml
- name: Optional check
  continue-on-error: true
  run: ./optional-check.sh
```

Use this only when failure of that step should not fail the job. Do not use it to hide important build or test failures.

## 5.18 `concurrency`

Controls overlapping workflow or job runs.

Example:

```yaml
concurrency:
  group: java-ci-${{ github.ref }}
  cancel-in-progress: true
```

For the same concurrency group, a newer run can cancel an in-progress run.

This can be useful when newer commits make older CI runs unnecessary.

## 5.19 `working-directory`

Sets the directory in which a shell command runs.

Example:

```yaml
- name: Run frontend tests
  run: npm test
  working-directory: frontend
```

This is useful when your repository contains multiple applications in separate directories.

## 5.20 `artifacts`

An artifact is a file produced during a workflow run that you want to save or download later.

For example, you can upload a build output:

```yaml
- name: Upload JAR
  uses: actions/upload-artifact@v4
  with:
    name: spring-boot-jar
    path: target/*.jar
```

This uploads matching JAR files from the runner to the workflow's artifact storage.

Artifacts are useful for sharing build outputs, test reports, and other files between workflow jobs or with developers.

---

# 6. Complete GitHub Actions Workflow for a Java Project

Now let's combine the concepts into one practical workflow.

### Project structure

```text
student-marketplace/
├── src/
├── pom.xml
├── mvnw
└── .github/
    └── workflows/
        └── ci.yml
```

This example assumes your Java project uses Maven and has a working Maven Wrapper (`mvnw`).

### Complete `ci.yml`

```yaml
name: Java CI Pipeline

on:
  push:
    branches:
      - main
      - develop

  pull_request:
    branches:
      - main

  workflow_dispatch:

permissions:
  contents: read

concurrency:
  group: java-ci-${{ github.ref }}
  cancel-in-progress: true

jobs:
  build-and-test:
    name: Build and Test
    runs-on: ubuntu-latest
    timeout-minutes: 15

    steps:
      # Step 1: Download repository code
      - name: Checkout code
        uses: actions/checkout@v4

      # Step 2: Configure Java
      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '21'
          cache: maven

      # Step 3: Make Maven Wrapper executable
      - name: Prepare Maven Wrapper
        run: chmod +x mvnw

      # Step 4: Compile and run tests
      - name: Run tests
        run: ./mvnw --batch-mode clean verify

      # Step 5: Upload build output if the build succeeds
      - name: Upload JAR artifact
        uses: actions/upload-artifact@v4
        with:
          name: spring-boot-jar
          path: target/*.jar
          if-no-files-found: warn
```

### How does this workflow work?

**Step 1: Trigger**

The workflow runs when:

- Code is pushed to `main` or `develop`.
- A pull request targets `main`.
- A developer manually starts the workflow.

**Step 2: Permissions**

```yaml
permissions:
  contents: read
```

Gives the workflow token read access to repository contents.

**Step 3: Concurrency**

```yaml
concurrency:
  group: java-ci-${{ github.ref }}
  cancel-in-progress: true
```

Avoids wasting resources on an older in-progress run in the same concurrency group.

**Step 4: Runner**

```yaml
runs-on: ubuntu-latest
```

Requests a GitHub-hosted Ubuntu runner.

**Step 5: Checkout**

```yaml
uses: actions/checkout@v4
```

Checks out the repository code.

**Step 6: Java setup**

```yaml
uses: actions/setup-java@v4
```

Configures Java 21 and enables Maven dependency caching.

**Step 7: Build and test**

```yaml
run: ./mvnw --batch-mode clean verify
```

Uses the Maven Wrapper to clean, compile, test, and perform the verification lifecycle.

**Step 8: Upload artifact**

```yaml
uses: actions/upload-artifact@v4
```

Uploads JAR files found in `target/` when the preceding build and test step succeeds.

If the tests fail, the upload step is skipped by default.

### Expected result

```text
Push code / Open pull request
             |
             v
      GitHub Actions
             |
             v
        Checkout code
             |
             v
        Set up Java 21
             |
             v
       Maven clean verify
             |
        +----+----+
        |         |
      Pass       Fail
        |         |
        v         v
  Upload JAR   Show failure
```

**Important:** This workflow builds and tests the application. It does not deploy it to a live server. Deployment requires additional steps and appropriate credentials.

---

# 7. Can We Create Multiple YAML Files?

Yes. You can create multiple workflow files inside `.github/workflows/`.

Example:

```text
.github/
└── workflows/
    ├── ci.yml
    ├── deploy.yml
    ├── security.yml
    └── scheduled-checks.yml
```

Each file defines a separate workflow.

For example:

- `ci.yml`: Build and test the application.
- `deploy.yml`: Deploy the application.
- `security.yml`: Run security checks.
- `scheduled-checks.yml`: Run scheduled maintenance or checks.

You do not need to split every task into a separate file. Multiple related jobs can live in one workflow.

Also remember that separate workflow files do not automatically execute one after another. Each workflow needs an appropriate trigger or an explicit mechanism to invoke another workflow.

---

# 8. Common Mistakes Beginners Make

1. **Wrong file location:** Workflows must be placed in `.github/workflows/`.
2. **Invalid YAML indentation:** YAML relies on indentation. Use consistent spaces.
3. **Confusing `uses` and `run`:** `uses` invokes an action; `run` executes shell commands.
4. **Forgetting job dependencies:** Jobs run in parallel by default. Use `needs` when one job must wait for another.
5. **Hardcoding secrets:** Store sensitive credentials in GitHub Secrets or environment secrets.
6. **Using the wrong Java version:** Configure a version compatible with your project.
7. **Assuming CI means deployment:** Building and testing do not automatically publish the application.
8. **Ignoring failed runs:** Inspect the Actions logs to find the command or step that failed.
9. **Using excessive permissions:** Grant only the repository permissions the workflow actually needs.
10. **Assuming all runners are identical:** Check the runner image and installed tools when debugging environment-specific failures.

---

# 9. Interview Questions

### Beginner

**Q1. What is GitHub Actions?**

GitHub Actions is GitHub's automation platform for executing workflows in response to events, such as pushes, pull requests, or scheduled triggers.

**Q2. What is a workflow?**

A workflow is an automated process defined in a YAML file inside `.github/workflows/`.

**Q3. What is a runner?**

A runner is the machine that executes a workflow job.

**Q4. What is the difference between `uses` and `run`?**

`uses` invokes an action, while `run` executes a shell command.

**Q5. What is the difference between a job and a step?**

A job is a group of steps executed on a runner. A step is an individual operation within that job.

### Intermediate

**Q6. What is the purpose of `needs`?**

It defines dependencies between jobs so that a dependent job waits for the required jobs to finish.

**Q7. What is a matrix strategy?**

It runs a job across multiple configurations, such as different Java versions.

**Q8. How do you store secrets?**

Use GitHub Secrets or environment secrets and access them through expressions such as `${{ secrets.DEPLOY_TOKEN }}`.

**Q9. What is `workflow_dispatch`?**

It enables manual workflow execution from GitHub, subject to workflow availability and permissions.

**Q10. How can you restrict workflow permissions?**

Use the `permissions` key and grant only the access needed by the workflow.

**Q11. Do jobs run sequentially by default?**

No. Jobs can run in parallel unless dependencies or other execution constraints control their order. Steps inside a job run sequentially by default.

**Q12. What is the difference between an artifact and a cache?**

An artifact stores outputs you want to retrieve or share, such as JAR files or test reports. A cache reuses dependencies or other files to speed up later workflow runs.

---

# 10. Quick Revision Cheat Sheet

| Keyword | Remember it as |
|---|---|
| `name` | Name of workflow, job, or step |
| `on` | Trigger events |
| `push` | Code pushed |
| `pull_request` | Pull request event |
| `workflow_dispatch` | Manual trigger |
| `schedule` | Scheduled trigger |
| `jobs` | Work to perform |
| `runs-on` | Machine to use |
| `steps` | Individual operations |
| `uses` | Invoke an action |
| `run` | Execute a command |
| `with` | Configure action inputs |
| `env` | Environment variables |
| `needs` | Job dependencies |
| `if` | Conditional execution |
| `permissions` | Token access permissions |
| `secrets` | Sensitive configuration |
| `strategy.matrix` | Multiple configurations |
| `outputs` | Pass values between steps or jobs |
| `timeout-minutes` | Execution time limit |
| `continue-on-error` | Allow execution to continue after failure |
| `concurrency` | Control overlapping runs |
| `working-directory` | Directory for a shell command |
| `upload-artifact` | Save workflow output files |

---

# 11. Final Summary

Remember these four concepts first:

1. **Workflow:** The complete automation process.
2. **Event:** What triggers the workflow.
3. **Job:** A group of tasks executed by a runner.
4. **Step:** One operation inside a job.

And remember the most common workflow pattern:

```text
Event
  |
  v
Workflow
  |
  v
Job
  |
  v
Runner
  |
  v
Steps
  |
  +-- uses: invoke an action
  |
  +-- run: execute a command
```

Start by creating a Java CI workflow that checks out your code, configures Java, runs Maven tests, and saves the build artifact. Once that works, you can extend it with Docker builds, security scans, and automated deployment.

Official documentation: https://docs.github.com/en/actions
