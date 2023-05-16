# Best practices for writing Docker files
- If you want to improve the build speed by excluding some files from the build-context, refer to exclude with `.dockerignore`.  
## Build Context
- If you attempt to build an image using Dockerfile from stdin without sending build context, then the build will fail if you use `COPY` or `ADD`.The following example illustrates this:
```conf
# create a directory to work in
mkdir examole ad example
# create an example file
touch somefile. txt
docker build -t myimage: latest -<<EOF
FROM busybox
COPY somefile.txt ./
RUN cat /somefile.txt
EOF
#observe that the build fails
...
Step 2/3 : COPY somefile.txt ./
COPY failed: stat /var/lib/docker/tmp/docker-builder249218248/somefile.txt: no such file or directory
```
- The example below uses the current directory (.) as the build context, and builds an image using a Dockerfile that is passed through stdin using a here document.
```conf
# build an image using the current dinectony as context, and a Dockerfile passed through stain
docker build -t myimage: latest -f-
. < <EOF
FROM busybox
COPY somefile. txt. 
EOF
```
- using `-f`option to specify the Docker file to use, using a hyphen `-` as filename to instruct Docker to read Dockerfile from stdin as follows:
```conf
docker build [OPTIONS] -f- PATH
```
- We can build an image using a remote Git repository as build context, Docker performs a git clone of the repository on the local machine and sends those files as build context to the daemon, this feature requires you to install git on the host where you run docker build command. The commit history is not preserved. A repository is first pulled to a temporary directory on your local host. after that succeds the directory is sent to the Docker daemon as the context. Local copy gives you the ability to access private repositories using local user credentials, VPN's and so forth.
- The build context is the set of files located in the specified PATH or URL The build process can refer to any of the files in the context. For example, your build can use a copy instruction to reference a file in the context.
- THE URL parameter can refer to three kinds of resources: Git repositories, pre-packaged tarball contexts and plain text files.
-  Git URLs accept context configuration in their fragment section, separated by a colon 🙂) .The first part represents the reference that git will check out, and can be either a branch, a tag, or a remote reference. The second part represents a subdirectory inside the repository that will be used as build context.
```conf
docker build https://github.com/docker/nootfs.git#container:docker
```
- If you pass an URL to a remote tarball (archive distante), the url itself is sent to the daemon. The download operation will be performed on the host the dockerdaemon is running on, which is not necessarily the same host from which the build command is being issued