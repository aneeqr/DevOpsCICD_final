# DevOpsCICD_final
 
 
 ## CREATE AZURE RESOURCES
 
 1. Create Azure Resources
 * [Microsoft custom templates](https://docs.microsoft.com/en-us/learn/modules/implement-cicd-iot-edge/4-exercise-create-ci-pipeline-iot-edge)- Create Resource Group, Iot Hub and Container Registry.
 ![image](https://user-images.githubusercontent.com/32796589/112735927-e22cc680-8f46-11eb-80c6-0e8baae199d2.png)

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
 
