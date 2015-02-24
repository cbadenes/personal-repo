Steps to deploy your maven artifact in your github project:  
##### 1) Add deployment information to your `pom.xml`:
```xml
<distributionManagement>
    <repository>
        <id>internal.repo</id>
        <name>Temporary Staging Repository</name>
        <url>file://${project.build.directory}/mvn-repo</url>
    </repository>
</distributionManagement>
 
<plugins>
    <plugin>
        <artifactId>maven-deploy-plugin</artifactId>
        <version>2.8.1</version>
        <configuration>
            <altDeploymentRepository>internal.repo::default::file://${project.build.directory}/mvn-repo</altDeploymentRepository>
        </configuration>
    </plugin>
</plugins>
```
##### 2) Get a personal access token from Github. 
Enter in Github: `Settings/Applications/Personal access tokens` and click on `Generate New Token`.  
##### 3) Copy that token as password in your maven config file
Edit `<M2_HOME>/conf/settings.xml` and include:  
```xml
<settings>
  <servers>
    <server>
      <id>github</id>
      <password>TOKEN</password>
    </server>
  </servers>
</settings>
```  
##### 4) Add the `site-maven-plugin` to upload your library defining your repository name and github user:  
```xml
<build>
    <plugins>
        <plugin>
            <groupId>com.github.github</groupId>
            <artifactId>site-maven-plugin</artifactId>
            <version>0.10</version>
            <configuration>
                <message>Library of ${project.version}</message>
                <noJekyll>true</noJekyll>
                <outputDirectory>${project.build.directory}/mvn-repo</outputDirectory>
                <branch>refs/heads/mvn-repo</branch>
                <includes><include>**/*</include></includes>
                <merge>true</merge>
                <repositoryName>REPOSITORY-NAME</repositoryName>
                <repositoryOwner>GITHUB-USERNAME</repositoryOwner>
                <server>github</server>
            </configuration>
            <executions>
              <execution>
                <goals>
                  <goal>site</goal>
                </goals>
                <phase>deploy</phase>
              </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```
##### 5) Finally, Upload your library:  
`mvn clean deploy`    
You can check that a new branch has been created named `mvn-repo` that contains your library ;)

##### 6) In any project that uses this library, you must include the reference to this repository:  
```xml
<repositories>
    <repository>
        <id>PROJECT-NAME-mvn-repo</id>
        <url>https://raw.github.com/USERNAME/PROJECT-NAME/mvn-repo/</url>
        <snapshots>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
        </snapshots>
    </repository>
</repositories>
```
That's all!!
