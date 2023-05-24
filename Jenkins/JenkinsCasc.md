# Jenkins Casac:
- **Jenkins Infrastructure** We can manage jenkins using many different external tools like:
    - Jenkins CLI
    - REST APT
    - Python-jenkins
    - jenkins-client (java, golang)
    - Ansible, chef, puppet
    - Docker
- **Jenkins job configuration**:
    - JobDSL plugin (groovy)
    - Job builder plugin (yaml)
    - Jenkins pipeline: Multi-branch, organizations folders
- First the configuration as code plugin must be installed on the Jenkins Controller, if you do not see the Confi system configuration section of the Manage Jenkins page, you need to install the plugin
```conf 
jobs:
    - script: >
        folder('folder_ name')
    - script:
    pipelineJob('folder_name/job_name') {
        definition {
          cpsScm {
            scriptPath 'path_to_jenkins_file'
            lightweight true
            scm {
                git {
                    remote {
                        url 'git_url_repo'
                    }
                branch '*/master'
                extensions {}
                }
            }
        }
      }
    }
```
- **`pipelineJob`**: Creates or updates a pipeline job
- **`SCM`**: SCM stands for Source Code Management.
- **`cpsScm`**: Load a pipeline script from SCM.
- **`lightweight`**: if selected, try to obtain the pipeline script content directly from sem without performing a full checkout false.
