# DevOpsCICD_final
 
 
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
  ```{r}
 ![image](https://user-images.githubusercontent.com/32796589/112736541-684b0c00-8f4b-11eb-981c-033e025683f4.png)

