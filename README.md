# DevOpsCICD_final

## OVERALL FLOWCHART

![image](https://user-images.githubusercontent.com/32796589/112736777-24f19d00-8f4d-11eb-87f1-8df07520a779.png)


[Reference](https://medium.com/marcus-tee-anytime/azure-iot-edge-with-azure-devops-manage-intelligent-edge-devices-with-automated-pipeline-bd98d9388b)
 
 
 ## CREATE AZURE RESOURCES
 
 1. Create Azure Resources
 * [Microsoft custom templates](https://docs.microsoft.com/en-us/learn/modules/implement-cicd-iot-edge/4-exercise-create-ci-pipeline-iot-edge)- Create Resource Group, Iot Hub and Container Registry and device provisioning service.
![image](https://user-images.githubusercontent.com/32796589/112736469-dd6a1180-8f4a-11eb-894f-80d804922a69.png)


 2. Create the Iot Edge device identify after you have created the hub using Azure CLI.
 ```{r}
 az iot hub device-identity create --hub-name IoTEdgeDevOps-iothub-qxw --device-id sp3 --edge-enabled
 ```
 3. Create a VM for Iot Edge runtime using the following [command](https://docs.microsoft.com/en-us/learn/modules/deploy-prebuilt-module-edge-device/4-exercise-setup-communication). Use the following Azure CLI command to create your IoT Edge device based on the prebuilt [iotedge-vm-deploy template](https://github.com/Azure/iotedge-vm-deploy)
 ```{r}
az deployment group create \
--resource-group IoTEdgeResources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
--parameters adminUsername='azureuser' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
 ```
 Example:
  ```{r}
 az deployment group create \
--resource-group  IoTEdgeResources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='myiot678' \
--parameters adminUsername='azureuser' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id sp3 --hub-name IoTEdgeDevOps-iothub-qxw -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
 ``` 
4. Verify that you have the following resources in your resource group.
![image](https://user-images.githubusercontent.com/32796589/112736422-5ddc4280-8f4a-11eb-96e8-1d67e97f949b.png)
![image](https://user-images.githubusercontent.com/32796589/112736382-2077b500-8f4a-11eb-8a0b-87153ff7dea7.png)

5. Run the following commands to test the status of the IoT Edge device:
  ```{r}
  sudo systemctl status iotedge
  ```
  
 ![image](https://user-images.githubusercontent.com/32796589/112736541-684b0c00-8f4b-11eb-981c-033e025683f4.png)


 ## CREATE YOUR OBJECT DETECTION MODEL USING MICROSOFT COGNITIVE SERVICES
 
 
 1. Login to the [Custom Vision AI Portal](https://www.customvision.ai)
 2. Train your model.
 ![image](https://user-images.githubusercontent.com/32796589/112736608-f1624300-8f4b-11eb-8898-603a6daadfe9.png)
 3. Export your model.
 ![image](https://user-images.githubusercontent.com/32796589/112736672-5453da00-8f4c-11eb-9ef3-23f9e7985b68.png)
 ![image](https://user-images.githubusercontent.com/32796589/112736654-430acd80-8f4c-11eb-96a5-381deb0408f1.png)
 4. Choose export as Docker File and export as linux container.
 ![image](https://user-images.githubusercontent.com/32796589/112736701-91b86780-8f4c-11eb-96e5-c892650a59df.png)


 ## IOT EDGE SOLUTION — CUSTOM MODULES AND COMPILATION
 
 1. Download [Visual Studio Code](https://code.visualstudio.com/).
 2. Install [Azure Iot Tools extension](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) in Visual studio.
 3. Create an IoT Edge solution, with a custom python module. Watch this [https://www.youtube.com/watch?v=PrbpsnHU3CQ&t=2s] Video for more details.

### CAMERA MODULE

1. First, we create a module that read images from camera, and send to our classifier to perform inference. Here we are just going to have a static picture.
2. Check github for camera specific code.

### OBJECT DETECTION MODULE

1. Add a new Iot Solution using Visual Studio Code. ![image](https://user-images.githubusercontent.com/32796589/112736940-85cda500-8f4e-11eb-8b07-f7d0e6a07c50.png)
2. Unzip the file we downloaded from Custom Vision, and copy to the folder contains module above. There are 4 files copied, as shown below:

![image](https://user-images.githubusercontent.com/32796589/112736968-b31a5300-8f4e-11eb-83f3-01583c770af9.png)

3. If you expand “app”, you will see a python script to perform prediction, as well as the model exported from custom vision. If you notice, there are several Dockerfile over here. Tell IoT Edge to use “Dockerfile” (The one without other extension) during compilation by modifying **“module.json”**

 ```{r}   
{
  "$schema-version": "0.0.1",
  "description": "",
  "image": {
    "repository": "iotedgedevopsqxw.azurecr.io/medium_classifier",
    "tag": {
      "version": "0.0.1",
      "platforms": {
        "amd64": "./Dockerfile",
        "amd64.debug": "./Dockerfile"
      }
    },
    "buildOptions": [],
    "contextPath": "./"
  },
  "language": "python"
}
```
4. Now we will import our repo in the DevOps and build a CI/CD pipeline.

## PUSH ALL CODE TO GITHUB

Push your code in your visual studio workspace to Github.

## DevOps — Repo and Pipeline

1. Navigate to [DevOps](https://dev.azure.com)
2. Create a new Organisation and create a new project inside it with visibility private.
![image](https://user-images.githubusercontent.com/32796589/112737112-e1e4f900-8f4f-11eb-83f6-37e248a8a56a.png)
3. Go to Repos and Files and click on import a new repository.
![image](https://user-images.githubusercontent.com/32796589/112737138-13f65b00-8f50-11eb-8114-1115330d8384.png)
4. Enter the link to your github repo.
![image](https://user-images.githubusercontent.com/32796589/112737225-aeef3500-8f50-11eb-8a4b-961e3ad5992a.png)
5. Go to pipelines and press use the classic editor.
![image](https://user-images.githubusercontent.com/32796589/112737251-e8c03b80-8f50-11eb-9dc9-0813465c7ad2.png) 
6. Select Azure repos Git and press continue.
![image](https://user-images.githubusercontent.com/32796589/112737270-1dcc8e00-8f51-11eb-8bc9-392a64453ab0.png)
7. Select your repo, and start with **“Empty job”** on the template page. This is the place for us to configure automation steps via the agent hosted in cloud.
![image](https://user-images.githubusercontent.com/32796589/112737292-4d7b9600-8f51-11eb-8d94-c2e1c4e76507.png)
8. Select Agent specification as Ubuntu-18.04 or 16.04. 
![image](https://user-images.githubusercontent.com/32796589/112737300-66844700-8f51-11eb-84cf-80712307d711.png)
9. In the tasks section, Click on **“+”* to add tasks, navigate to Marketplace, and search for **“Azure IoT Edge”**
![image](https://user-images.githubusercontent.com/32796589/112737335-b531e100-8f51-11eb-8643-b3751664988e.png)
10. Add the module. Under **“Action”**, select **“Build module images”**. Browse your repo to indicate the **“deployment.template.json”** as well. Leave the rest default
![image](https://user-images.githubusercontent.com/32796589/112737385-15c11e00-8f52-11eb-879c-91fbc29011c8.png)
11. Add a new task using the **“Azure IoT Edge”** extension. After building images, we need to push to our Azure Container Registry. Now, the action change to **“Push module images”**
![image](https://user-images.githubusercontent.com/32796589/112737486-d515d480-8f52-11eb-996b-4042e21e2f1f.png)
12. Copy files to our artifact repository for deployment. Add a task called **“Copy files”**. Select your repository as Source Folder, enter the following 2 lines for contents that we need to copy, and **“$(Build.ArtifactStagingDirectory)”** as our target folder.
![image](https://user-images.githubusercontent.com/32796589/112737575-73099f00-8f53-11eb-8e45-bf7ddb0cb830.png)
13. Finally, we need to publish artifacts. Add a new task using **“Publish build artifacts”.** Ensure the path to publish is same as above, and put **“drop”** as Artifact name.
![image](https://user-images.githubusercontent.com/32796589/112737723-74879700-8f54-11eb-8541-f5967e589a8d.png)
14. Enable Continous integration
![image](https://user-images.githubusercontent.com/32796589/112737729-8406e000-8f54-11eb-89c9-611a523c4f59.png)
15. Go to variables and enter your container registery details (CONTAINER_REGISTRY_USERNAME,CONTAINER_REGISTRY_PASSWORD,CONTAINER_REGISTRY_ADDRESS). These can be found on portal
![image](https://user-images.githubusercontent.com/32796589/112737895-0643d400-8f56-11eb-9cf4-08b03b2e2b3c.png)
![image](https://user-images.githubusercontent.com/32796589/112737855-9b929880-8f55-11eb-8993-1bd443d0335c.png)
![image](https://user-images.githubusercontent.com/32796589/112737869-c54bbf80-8f55-11eb-9488-4ea29a89c05c.png)
![image](https://user-images.githubusercontent.com/32796589/112737815-3474e400-8f55-11eb-8c02-6e2f1ebc2108.png)
16.
