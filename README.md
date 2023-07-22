# stub-response-custom-policy

### Introduction
Mulesoft Policies can enforces security using whitelisting or blacklisting IPs/CIDR ranges, traffic contoll using SLA, authentication and threat protrctions etc. Custome policies are Out-Of the Box polcies and can anyone implemente to enhance existing or new functionalities based on the use cases.

Below are Custom Policy process flow,
1. Creation of Custome policy uisng maven archetype
2. Implementation of policy
3. Packahging a policy
4. Deploy or publishing a polciy to exchange
5. Applying policy to APIs through API manager

## Setting up a maven archetype

 First step to setting up a maven archetype to create specified files which required for custom polciy creation. Configure below Mavne Architype in *settings.xml* file

 ```
 <profiles>
   <profile>
     <id>archetype-repository</id>
     <repositories>
       <repository>
         <id>archetype</id>
         <name>Mule Repository</name>
         <url>https://repository.mulesoft.org/nexus/content/repositories/public</url>
         <releases>
           <enabled>true</enabled>
           <checksumPolicy>fail</checksumPolicy>
         </releases>
         <snapshots>
           <enabled>true</enabled>
           <checksumPolicy>warn</checksumPolicy>
         </snapshots>
       </repository>
     </repositories>
   </profile>
 </profiles> 
 ```
## Project settup using archetyp

After Maven confoguration, execute below maven command to create a custom polciy with default files
 ```bash
mvn -Parchetype-repository archetype:generate -DarchetypeGroupId=org.mule.tools -DarchetypeArtifactId=api-gateway-custom-policy-archetype -DarchetypeVersion=1.2.0 -DgroupId=${groupId} -DartifactId=${polciyname} -Dversion=1.0.0-SNAPSHOT -Dpackage=mule-policy
```
Here,
 1. {groupId} is nothing but organization gorup id from anypoint cloudhub where policy to deploy. organization Id is like UUID of Mulesoft anypoint platform account. Example org id: b618b9ea-aeb6-44f5-8dde-f74124437858
 2. {policyName} name of the polciy for the desired functionality.

Command prompts to provide below propts to provide,
policyName and Policy description like as below,

policyName:helloworld-custom-policy
Policy Description: Hello World Custom Policy

Afetr completion of command prompt it generates below files in respective directories,
 ```
hellowork-custom-policy/
├── hellowork-custom-policy.yaml
├── mule-artifact.json
├── pom.xml
└── src
   └── main
       └── mule
           └── template.xml
 ```
The above four files are required to run a custom policy.

1. `pom.xml` contains below elements which required,
    - *groupId*: To represent a organization id 
    - *mule-policy*:  To package build file as mule-policy, 
    - *mule-maven-plugin*: For creating a deployable jar file,         
    - *maven-deploy-plugin*: For deploying a deployable jar file into exchange.
    - *distributionManagement*: To represent Users exhange.
2. `helloworld-custom-policy.yaml` to redners the yaml file data to UI
3. `template.xml` file to provide the logic of policy to perform
4. `mule-artifact.json` this file is prvoide minor version of runtime and securing any sensitive information in UI.

template.xml file contains following code snippet,

 ```
 <http-policy:proxy name="{{{policyId}}}-custom-policy"> --> (i)
        <http-policy:source>  --> (ii)

            <http-policy:execute-next/> --> (iii)

            <http-transform:set-response statusCode="201">
                <http-transform:body>#[ 'Hello World!' ]</http-transform:body>
                <http-transform:headers>#[ {'New-Header': 'Hello World!'} ]</http-transform:headers>
            </http-transform:set-response>

        </http-policy:source>
    </http-policy:proxy>
 ```

Here, 
- `(i)` represents basic structure of policy. {{{{policyId}}} refers handlebar variable and reads data from configuartion YAML file.
- `(ii)` represents to refer HTTP inboud or HTTP outbout logic which needs to be perform either before or after flow or next policy.
- `(iii)` which indicates tha the next policy or flow should be exeuted.

As per the above logic, overriding the result of API as "Hello World!" and status code as 201 irrespective of the response.

![image](https://github.com/KathaSudharshan/mule-custom-policies/assets/138109855/1859a7dc-c04f-4104-b636-d658114878e3)


### Packaging a Policy ###
After completion of custom policy implemenation, package a policy to deploy into exchange. Use below maven command to create a JAR file,
 ```bash
 mvn clean package
  ```
 ![image](https://github.com/KathaSudharshan/mule-custom-policies/assets/138109855/d0c168d5-0041-46f8-ad54-5fd61b13280d)
![image](https://github.com/KathaSudharshan/mule-custom-policies/assets/138109855/2e541204-a537-4e38-86fe-1ef73951d4c8)


### Deploy policy in exchange ###

To deploy ploicy in Anypoint Exchange execute below command

 ```bash
 mvn deploy
  ```
![image](https://github.com/KathaSudharshan/mule-custom-policies/assets/138109855/b9d63e56-2639-433f-b693-ab34ff06a969)
![image](https://github.com/KathaSudharshan/mule-custom-policies/assets/138109855/82ebd0c3-4701-421d-b7fc-52dff2ff49eb)

After successfull deploymnet custom policy will visible in anypoint platform API Manager. Like as below
![image](https://github.com/KathaSudharshan/mule-custom-policies/assets/138109855/130684a6-0d63-4018-9a73-c372de5630f3)

Apply policy to any of the API and observe the response of API result and it shows as shown below,

API response before applyoing a policy,
![image](https://github.com/KathaSudharshan/mule-custom-policies/assets/138109855/4c2664f9-39cc-410a-a2d2-d0d346f1ea3f)

API response after applying a policy,
![image](https://github.com/KathaSudharshan/mule-custom-policies/assets/138109855/f34002cd-01b6-4135-a172-cbd2b43df3d8)
![image](https://github.com/KathaSudharshan/mule-custom-policies/assets/138109855/4f437d8d-6c8b-4ff5-a0c1-3f80a86d0485)
new header as well added in response

## Contributing

Contributions are always welcome!

See `contributing.md` for ways to get started.

Please adhere to this project's `code of conduct`.


## Feedback

If you have any feedback, please reach out to us at k.sudha499@gmail.com
