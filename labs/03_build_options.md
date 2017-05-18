Lab 3: Build Options/Properties
===============================

When using multibranch pipelines you can no longer configure build options (declarative syntax)
or build properties (scripted syntax) through the web interface. For single branch ``Jenkinsfile``
pipelines configuring build options/properties in the ``Jenkinsfile`` is recommended as well in
order to obtain a single source of truth <https://en.wikipedia.org/wiki/Single_source_of_truth>.
This lab shows how this is done.

Lab 3.1: Build Options
----------------------

In declarative pipelines build options/properties are configured through the ``options`` directive.
Only a single ``options`` directive is allowed and must be contained in the ``pipeline`` block. The available options are documented in the [Pipeline Syntax](https://jenkins.io/doc/book/pipeline/syntax/#options) section.
Create a new branch named ``lab-3.1`` from branch ``lab-2.1`` and change the contents of the ``Jenkinsfile`` to:

```groovy
pipeline {
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        timeout(time: 10, unit: 'MINUTES')
        timestamps()  // Timestamper Plugin
    }
    stages {
        stage('Greeting') {
            steps {
                echo 'Hello, World!'
            }
        }
    }
}
```

This pipeline is configured to:
* Keep a maximum of **5** builds
* Timeout builds that run longer than **10 minutes**
* Print timestamps before each build log line

**Note:** Verify on the jenkins master, whether the new options are now visible in the configuration view.

Lab 3.2: Build Properties (Scripted Syntax)
-------------------------------------------

In scripted pipelines build options/properties are configured through the ``properties`` step.
The ``properties`` step replaces any existing build properties with the list given,
therefore only a single instance should be used per pipeline.
Create a new branch named ``lab-3.2`` from branch ``lab-2.2`` and change the contents of the ``Jenkinsfile`` to:

```groovy
properties([
  buildDiscarder(logRotator(numToKeepStr: '5'))
])

timestamps() {
    timeout(time: 10, unit: 'MINUTES') {
        node {
            stage('Greeting') {
                echo 'Hello, World!'
            }
        }
    }
}
```

This configures the same job properties as the declarative pipeline in lab 3.1. 
In scripted pipelines job properties are configured through steps and the usage
of a step depends on its implementation. Use the "Snippet Generator" to find
the correct syntax. Scripted pipelines need to run before Jenkins can pick
up changes in build properties. Multibranch pipeline support takes care of this
an runs any new jobs and any jobs with changes in their ``Jenkinsfile``.

**Note:** Verify on the jenkins master, whether the new options are now visible in the configuration view.
