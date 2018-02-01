## Jenkins

#### 入门
This guided tour will use the "standalone" Jenkins distribution which requires Java 8. A system with more than 512MB of RAM is also recommended.
* [Download Jenkins](http://mirrors.jenkins.io/war-stable/latest/jenkins.war).
* Open up a terminal in the download directory and run `java -jar jenkins.war`
* Browse to `http://localhost:8080` and follow the instructions to complete the installation.
* Many Pipeline examples require an installed Docker on the same computer as Jenkins.


#### Jenkins (Ubuntu/Debian)
```
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
```
This package installation will:
* Setup Jenkins as a daemon launched on start. See `/etc/init.d/jenkins` for more details.
* Create a `jenkins` user to run this service.
* Direct console log output to the file `/var/log/jenkins/jenkins.log`. Check this file if you are troubleshooting Jenkins.
* Populate `/etc/default/jenkins` with configuration parameters for the launch, e.g `JENKINS_HOME`
* Set Jenkins to listen on port `8080`. Access this port with your browser to start configuration.


#### Jenkins (Docker)
`docker pull jenkins/jenkins`

Next, run a container using this image and map data directory from the container to the host; e.g in the example below `/var/jenkins_home` from the container is mapped to `jenkins/` directory from the current path on the host. Jenkins `8080` port is also exposed to the host as `49001`.
```
docker run -d -p 49001:8080 -v $PWD/jenkins:/var/jenkins_home -t jenkins/jenkins
```
