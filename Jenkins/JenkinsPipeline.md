# What is Jenkins Pipeline?
- Jenkins Pipeline (or simply "Pipeline" with a capital "P") is a suite of plugins which supports implementing and integrating continuous delivery pipelines into Jenkins.

-> Pipeline provides an extensible set of tools for modeling simple-to-complex delivery pipelines "as code" via the Pipeline domain-specific language (DSL) syntax.

# Why Pipeline?
- **Code:** Pipelines are implemented in code and typically checked into source control, giving teams the ability to edit, review, and iterate upon their delivery pipeline.

- **Durable:** Pipelines can survive both planned and unplanned restarts of the Jenkins controller.

- **Pausable:** Pipelines can optionally stop and wait for human input or approval before continuing the Pipeline run.

- **Versatile:** Pipelines support complex real-world CD requirements, including the ability to fork/join, loop, and perform work in parallel.

- **Extensible:** The Pipeline plugin supports custom extensions to its DSL and multiple options for integration with other plugins.

# Declarative versus Scripted Pipeline syntax

**A Jenkinsfile can be written using two types of syntax - Declarative and Scripted.**

Declarative and Scripted Pipelines are constructed fundamentally differently. Declarative Pipeline is a more recent feature of Jenkins Pipeline which:

- Provides richer syntactical features over Scripted Pipeline syntax, and
is designed to make writing and reading Pipeline code easier.

Many of the individual syntactical components (or "steps") written into a Jenkinsfile, however, are common to both Declarative and Scripted Pipeline.
- **Freestyle jobs**: With Freestyle jobs you cannot configure your kubernetes pod per job. you're limited to the global pod templates you've configured.
- **Pipeline jobs**: Pipeline jobs provide additional flexibility, allowing you to define the pod template in the job itself, allowing for much more flexibility (including running multiple containers in a pod )