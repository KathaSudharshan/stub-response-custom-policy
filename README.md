# stub-response-custom-policy

### Introduction
Mulesoft Policies can enforces security using whitelisting or blacklisting IPs/CIDR ranges, traffic contoll using SLA, authentication and threat protrctions etc. Custom policies are Out-Of the Box polcies and can anyone implement to enhance existing or new functionalities based on different use cases.

This is a stud-response-custom-policy and can bed used to provide mock response of application/system without generating traffic to target/backend systems. In case of Payment gateway integration  or if target system does not support mutilple enviornments for testing like in SIT/UAT/Performance testing phases then insetad of passing traffic to target systems need to mock traffic inbetween integration layer and havee to retrun standard respons every time from the respective system. So that SIT/UAT/Perofmance testing teams can proceed thier testing without involvement of respective target systems. For these cases this policy helps to omit the traffic to target systems. This policy has a feasibility to mock responses based on condition checks otheriwse for every request.

This policy has following fetaures,
1. Dynamic Request condiotion checks if required to process specific type of request to target systems.
2. Setup Statuc Codes as required
3. Response Payload can setup as required

Below are attribuites have for this policy,

1. Evaluvate Request : Its a boolean property. If its true then will see Request Condition to apply the policy for specific requests otherwise applicable to all traffic.
2. Request Condition: To evaluate condition whether result is true or flase.
3. Status Code: To set up status code for the stub response
4. Resposne Payload: otherwise will only see Status Code and Response payload text boxes. If condition statisifies then  satisifies then only returns stub resposne otherwise traffice flows to target system.
5. advanced options: has a feasibility whether this policy applied for all operations or specific operations.

Below is the screenshot of policy UI,

![image](https://github.com/KathaSudharshan/stub-response-custom-policy/assets/138109855/56544013-7b0f-48af-8c64-fdbb28945bf2)


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

policyName:stub-response-custom-policy
Policy Description: Stub response custom policy

Afetr completion of command prompt it generates below files in respective directories,
 ```
hellowork-custom-policy/
├── stub-response-custom-policy.yaml
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
2. `stub-response-custom-policy.yaml` to redners the yaml file data to UI
3. `template.xml` file to provide the logic of policy to perform
4. `mule-artifact.json` this file is prvoide minor version of runtime and securing any sensitive information in UI.

template.xml file contains following code snippet,

 ```
 <http-policy:proxy name="{{{policyId}}}-custom-policy"> --> (i)
        <http-policy:source>  --> (ii)

            <http-policy:execute-next/> --> (iii)

        </http-policy:source>
    </http-policy:proxy>
 ```

Here, 
- `(i)` represents basic structure of policy. {{{{policyId}}} refers handlebar variable and reads data from configuartion YAML file.
- `(ii)` represents to refer HTTP inboud or HTTP outbout logic which needs to be perform either before or after flow or next policy.
- `(iii)` which indicates tha the next policy or flow should be exeuted.

### Overview of using this policy.
![image](https://github.com/KathaSudharshan/stub-response-custom-policy/assets/138109855/557063af-3500-4f36-b26a-4af24b3cd0f6)

As per the above flow traffic flows to target system for every request and transaction will be processed to Worlpay to complete the payment process in production. Where traffice should not process to paymemt system for lower enviornments as its deducts payment of test account. Instead of deducting amount MuleSoft system api layer should retrun stud response every time for testing phases. 

Below is the high level design how it works,

![image](https://github.com/KathaSudharshan/stub-response-custom-policy/assets/138109855/eec38d36-d3d2-4adf-9cbb-44cc7cb3486b)

### Packaging a Policy ###
After completion of custom policy implemenation, package a policy to deploy into exchange. Use below maven command to create a JAR file,
 ```bash
 mvn clean package
  ```

### Deploy policy in exchange ###

To deploy ploicy in Anypoint Exchange execute below command

 ```bash
 mvn deploy
  ```
After successfull deploymnet custom policy will visible in anypoint platform API Manager. Like as below
![image](https://github.com/KathaSudharshan/stub-response-custom-policy/assets/138109855/34fcf196-1533-4ae8-b45c-f671c0f76b18)

Apply policy to any of the API and observe the response of API result and it shows as shown below,

Policy without verifiying simulate flag,
![image](https://github.com/KathaSudharshan/stub-response-custom-policy/assets/138109855/bff64f65-2791-4279-94de-6d12a8a351ae)


API response before applyoing a policy,
![image](https://github.com/KathaSudharshan/mule-custom-policies/assets/138109855/4c2664f9-39cc-410a-a2d2-d0d346f1ea3f)

API response after applying a policy,
![image](https://github.com/KathaSudharshan/stub-response-custom-policy/assets/138109855/09258dce-ae24-43dd-bc16-75831e072d15)
Stubbed Resposne body as (This is configurable in policy)
```bash
 {
  "id": 1234,
  "name": "Stub Response",
  "gender": "Male",
  "designation": "lead",
  "department": "Integration",
  "location": "UK"
}
  ```

new header as well added in response

## Contributing

Contributions are always welcome!

See `contributing.md` for ways to get started.

Please adhere to this project's `code of conduct`.


## Feedback

If you have any feedback, please reach out to us at k.sudha499@gmail.com
