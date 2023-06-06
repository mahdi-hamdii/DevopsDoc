- Pod template, on a le inherit from jenkins-inbound-agent, qui est une configuration YAML du pod qu'on va creé.
dans ce template on trouve un autre container qui est le jnlp (java network launch protocol), ce container assure la communication entre l'agent et le controlleur.
- le jnlp permet de faire un remote exec dans le Jenkins controller pour executer des fonctionialiter qui se trouve dans le controller et par la suite on recupere les resultat graçe au jnlp
- dans notre cas on utlise le websocket

# Jenkins Kubernetes Plugin:
1. Developer triggers build by pushing code to git.
2. Jenkins master tells the kubernetes cluster to give him an agent running
3. It will create the pod with the `Jnlp` (jenkins-agent) container and the other specified required containers.
4. When the Job is done it will delete the pod and free resources.
- Jenkinsfile is a text file that contains the definition of a Jenkins Pipeline and is checked into source control.
- `PodTemplate`: defines an ephemeral pod template. it is created while the pipeline execution is within
The following idiom creates a pod template with a generated unique label available as `POD_LABEL` and runs commands inside it. 
- `node (POD_LABEL)`: Jenkins will search for a pod with the specified label, if not found then it will create a new pod with a unique label in which it will execute the steps.
```conf
podTemplate(...){
    node (POD_LABEL) {
        // some steps
        }
}
```
- By default, commands will be executed in the `jnlp` container, where the Jenkins agent is running.
- Inside `containers` inside containerTemplate: we can modify:
    - `alwaysPullImage`: The container will pull the image upon starting
    - `command`: The command the container will execute. It will overwrite the docker entrypoint. A typical value is sleep
    -  `tty:true`: you enable TTY which is required for execution of a shell. 
    - `ttyEnabled: false`: can be beneficial in some cases. disabling TTY allocation can reduce resource usage and may improve pipeline
performance.
- To keep containers in running state when the default behavior defined in the entrypoint is to to run something and exit we can overide this behavior with `ttyEnabled` and `cat`
- The command `cat` as argument for the container means that the cat executable is run without further arguments. if you run cat on terminal yourself in this, you will notice that it does nothing, but blocks and waits for the input. The reason is that the cat command reads input from a file or stdin and prints it. If there is nothing it just waits. This is often the sole idea: start a container or pod and keep it running. it will be terminated by other means. You can think of it like `sleep infinity`
``` conf
podTemplate( 
    inheritFrom: 'jenkins-inbound-agent'
    containers: [
containerTemplate (name: 'docker-container' image: '', alwaysPullImage: false, ttyEnabled: true, command: 'cat'
containerTemplate (name: 'node -container', alwaysPullImage: false, ttyEnabled: true, command: 'cat'),
containerTemplate (name:'sonar-container' image:'', alwaysPullImage: false, ttyEnabled: true, command: 'cat')
    ]
)
```
# Inheritance:
- A pod template may or may not inherit from an existing template. This means that the pod template will inherit `node selector`, `service acount`,`image pull secrtes`, `container templates` and `volumes` from the templates it inherits from.
- Using the specified `yamIMergeStrategy` it will merge the podTemplate passed and the configuration used in Jenkinsfile:
     - `serviceAccount` and `nodeSelector` when are overriden completely substitute any possible value found on the parent
     - `container templates` that are added to podTemplate, that has a matching containerTemplate (a container template with the same name) in the `parent` template will inherit the config of the parent container template otherwise if no matching container is found, the template is added as is.
    - Volume inheritance works exactly as container templates
- We can inheritfrom one or multiple separated by space. In the later case each template will be processed in the order they apper in the list (later items overriding earlier ones). In any case if the referenced template is not found it will be ignored

# Other:
- `jnlp`: is the container inside the created jenkins job who is making the connection back to our jenkins controller.
- All containers have access to the same workspace. (if we create a file in first stage with container A, we will be able to cat that file in another stage with another container)

```conf
withEnv(["DOCKER_HOST=","DOCKER_TLS_VERIFY=1"]){
withCredentials([[$class:'DockerServerCredentialsBinding', credentialsId: "credential name", variable: 'DOCKER CERT PATH']]){
    //commands
    }
}
```
- `withEnv`: sets one or more environment variables within a block. The names of the environment variable are case insensitive but case-preserving. Setting Foo will change the value of FOO if it already exist.

- `DOCKER_TLS_VERIFY=1`: tells the `docker client` whether to communicate with the `docker daemon dockerd` with any TLS verificatio to 1, the server needs to have a private CA signed key pair, and the client also need to have a key pair signed by the same CA. It tells the client to verify that server key it receives is signed by the private CA. The daemon/server will have a similar setting client certificates.
- If `DOCKER_TLS_VERIFY` is set to anything other than 1, the docker client will not verify the registry's TLS certificate, which the connection vulnerable to man-in-middle attacks and other security risks.
- `DockerServerCredentialsBinding`: class in a jenkinsfile is used to bind docker server credentials in a variable in the build ent
- In the `withCredentials` block, the `class` parameter specifies the type of credential binding to be used. In our case the credentials being bound to the "DOCKER_CERT _PATH' environment variable, this allows the build to authenticate with a Docker Registery or Docker using the provided credentials. Behind the scenes, the `DockerServerCredentialsBinding` class leverages the docker java api to comm with the Docker Daemon and authenticate using the provided credentials.
- Overall, `DockerServerCredentialsBinding` class provides a secure and convenient way to authenticate and communicate with a Docker during a jenkins build.
# JNLP:
- **Jenkins Remoting**: is a library, and executable java archive, which implements the communication layer in Jenkins. This includes the TCP-based communication protocols, remote procedure calls, class loading, data streaming, etc. Currently remoting is primarly used in communications between the jenkins Controller and the jenkins agents.
- **JNLP**: `JAVA NETWORK LAUNCH PROTOCOL` is used to connect launch your java application (here jenkins) from a remote location. 
- JNLP image is for jenkins agents using TCP or WebSockets to establish inbound connection to the Jenkins master. This agent is powered by The jenkins remoting library
- **JAVA WEB START or JNLP**: it was designed for the sole purpose of automatically starting java applications from a remote location.  JAVA would attempt to reach out to the host described in the XML structure. Download the specified JAR package and if successful execute it.
