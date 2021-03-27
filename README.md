# DevOpsCICD_final
 
 
 ## CREATE AZURE RESOURCES
 
 1. Create Azure Resources
 * [Microsoft custom templates](https://docs.microsoft.com/en-us/learn/modules/implement-cicd-iot-edge/4-exercise-create-ci-pipeline-iot-edge)- Create Resource Group, Iot Hub and Container Registry.
 ![image](https://user-images.githubusercontent.com/32796589/112735927-e22cc680-8f46-11eb-80c6-0e8baae199d2.png)

 2. Create the Iot Edge device identify after you have created the hub.
 ```{r}
 az iot hub device-identity create --hub-name IoTEdgeDevOps-iothub-qxw --device-id sp3 --edge-enabled
 ```
 3. Create a VM using the following
