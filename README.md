# IoT Workshop 2017 - Australia 

Welcome to our IoT workshop! 

We are so glad to have you with us today. We put together this workshop to give you some hands-on experience with the Microsoft Azure IoT suite. Our goal is to get you up-to-speed on the latest developments so you can take the knowledge back to your office and start implementing IoT solutions with them.

## Prerequisites and Course Materials
This is a technical workshop and you're expected to be comfortable writing and understanding code (we will chiefly use Node.js throughout). Also, you're expected to be comfortable around Linux, be able to SSH in and know how to use text editors (such as **vi** or **nano**). Basic understanding of Azure is essential, but not critical.

Please make sure to complete the following prerequisites prior to coming to the workshop
- [Install](http://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) PuTTY (Windows users only)
- [Download](https://github.com/Azure/azure-iot-sdk-csharp/releases) Azure IoT Device Explorer (Windows only) or [Install](https://github.com/Azure/azure-iot-sdks/blob/mvp_summit/doc/manage_iot_hub.md#iothub-explorer) a command line utility  
- [Activate](https://azure.microsoft.com/en-au/offers/ms-azr-0044p/) a free trial Azure subscription and familiarise yourself with the [Azure Portal](https://portal.azure.com/). Spin up a linux VM, SSH in, resize it and don't forget to tear it or shut it down. Explore how to use keyboard shortcuts - they save time!
- [Download](media/slides-final-abridged.zip) Level 300 slides for your reference

## Agenda
Day 1: 
- Azure IoT Suite: Level 300 intro 
- Environment setup: azure subscription, SSH access to your Raspberry PI, set up prerequisites
- Lab 1: Azure IoT Device Management
- Lab 2: Azure IoT Gateway SDK
- Coming up with problems to solve on Day 2

Day 2:
- Hacking away!
- Wrap-up

## Getting Started
Each group will be provided with a Raspberry Pi 3 running Raspbian Jessie, a Texas Instruments(TI) 
Bluetooth Low Energy (BLE) Sensor Tag, and an Azure Subscription.

We are presenting two labs today, one for **Azure IoT Device Management** and 
one for the **Azure IoT Gateway SDK**.  The Azure IoT Device Management lab will introduce you to the concepts of Device Twins and Direct Methods. The Azure IoT Gateway SDK lab will introduce you to the open source SDK for building IoT Gateway devices enabling, for instance, non-internet connected device to send data to Azure IoT. 

> Proctors are available to help you work through these workshops, answer questions or talk tech.

Your Raspberry Pis comes with a stock standard version of Raspbian, with the SDKs preloaded, compiled and located in:
- Device SDK: `/home/pi/azure-iot-sdks`
- Gateway SDK: `/home/pi/azure-iot-gateway-sdk`

VNC server has been enabled, you can access you RPI's GUI using [VNC Viewer](https://www.realvnc.com/download/viewer/).

To save time, please skip all sections to do with installing raspbian, cloning git repos and compiling SDKs. Feel free to [download the Raspbian image (warning - 16GB)](https://iothack.blob.core.windows.net/image/16G%2020170516.img) we use here and write it to an SD card following [these instructions](https://www.raspberrypi.org/documentation/installation/installing-images/). 

## Lab 1: Azure IoT Device Management

This lab will demonstrate the **Device Twins** and **Direct Methods** features and is available [here](https://github.com/iizotov/azure-iot-sdks/tree/mvp_summit/c/serializer/samples/devicetwin_configupdate#how-to-update-configuration-and-reboot-an-iot-device-with-azure-iot-device-twins). 

The Device SDK GitHub repo has been cloned to `/home/pi/azure-iot-sdks/`. In the interest of time, **Step 1: Prerequisites**, **Step 2: Prepare Device** and the `sudo c/build_all/linux/setup.sh` step in **Step 3: Build and Run the Device Twins Sample** can be skipped.

> Please make sure you set the correct timezone by running `sudo raspi-config`

> Please make sure the time is synchronised by running `sudo service ntp stop; sudo ntpdate 0.ubuntu.pool.ntp.org; sudo service ntp start`

## Lab 2: Azure IoT Gateway SDK 

This lab will demonstrate the open source **Azure IoT Gateway SDK** using a Bluetooth Low Energy (BLE) Sensor Tag, Raspberry Pi and Node.js.

The Azure IoT Gateway SDK lab is available [here](iot-hub-gateway-sdk-physical-device.md).

> Please read the architectural introduction before jumping to the "Enable connectivity to the Sensor Tag device from your Raspberry Pi 3 device" section to configure your TI BLE Sensor Tag.

The Azure IoT Gateway SDK GitHub repo has been cloned to `/home/pi/azure-iot-gateway-sdk` and compiled. Steps that can be skipped are called out in the lab. The raw data values read from the SensorTag temperature sensor are: die (ambience) temperature and object temperature.

## Bonus Challenges

You are likely an overachiever, so we've included a few extra challenges!  Please make sure you complete the [Azure IoT Gateway SDK lab](iot-hub-gateway-sdk-physical-device.md) first.

### Decode Telemetry
- The SensorTag temperature telemetry requires decoding (i.e. conversion to JSON). The raw data comes as a 4-byte array consisting ot two unsigned 16 bit integers, one for the die (ambience) temperature and one for the object temperature measured using the IR temperature sensor. You have more than one option to decode it:
   * Wire up an Azure Function using your IoT Hub's Event Hub endpoint to convert temperature readings coming from the SensorTag and publish them to an Event Hub for further processing by Azure Stream Analytics. Use [this Google query](https://www.google.com.au/search?num=50&newwindow=1&espv=2&q=%22SCALE_LSB+sensortag%22) for ~~inspiration~~ code samples, [this blog](https://www.10thmagnitude.com/tech-blog/step-step-guide-creating-functions-within-azures-iot-hub/) for instructions and [this link](http://stackoverflow.com/questions/42003600/azure-iot-hub-eventhub-and-functions) for implementation details. Some more reading material [here](https://azure.microsoft.com/en-us/blog/how-to-use-azure-functions-with-iot-hub-message-routing/). Okay, if you're feeling lazy just [grab the code](./function.cs) from this repo
   * (Hardcore!) You can even do it on the gateway by building a new module in Node.JS. Refer to [Node.js Printer](https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/nodejs_simple_sample/nodejs_modules/printer.js) to understand how to consume messages and to [Node.js Simulated Device](https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/nodejs_simple_sample/nodejs_modules/sensor.js) to get an idea how to publish values. The flow can be be SensorTag -> mapper -> **Converter** -> IoTHub or SensorTag -> **Converter** -> mapper -> IoTHub. You will need to understand how the identity mapper and the IoTHub writer work, the documentation can be found [here](https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/iothub/devdoc/iothub.md) and [here](https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/identitymap/devdoc/identity_map.md). If you succeed at doing it then you can wrap the telemetry in a JSON message and connect Azure Stream Analytics directly to the IoT Hub.

### Create an Azure Stream Analytics Query
- Create an Azure Stream Analytics query that selects all the data from your Event Hub or IoT Hub (if you chose the hardcore path) and outputs the results to Power BI, displaying aggregate metrics and sending emails or texts (e.g. when temperature exceeds 37 degrees for longer that 15 consecutive seconds). Experiment with the ASA windowing functions and Azure Logic Apps to achieve it.
- How about making this threshold dynamic? Hint: store it as [reference data](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-use-reference-data) and use it in your queries.
- How about closing the loop and sending Cloud-to-device messages when the temperature is above a threshold... say, lighting up the red LED on the TI Tag? Hint: connect ASA to an Event Hub or Storage Queue and create a triggered Azure Function to push out the C2D message. Don't forget to implement the logic for turning the light off!

### Create a Power BI Dashboard
- Create a [Power BI](http://app.powerbi.com) Dashboard that visualizes your TI Sensor Tag data in creative ways.  Feel free to use any of the Power BI Custom Visuals available [here](https://store.office.com/en-us/appshome.aspx?productgroup=PowerBI). You can learn how to create Power BI Dashboards from a Stream Analytics Output [here](https://azure.microsoft.com/en-us/documentation/articles/stream-analytics-power-bi-dashboard/).

### Time Series Insights (Preview)
- Create a [Time Series Insights](https://azure.microsoft.com/en-us/services/time-series-insights/) environment and connect it to the IoT Hub

### More Sensors! 
- Using the [GATT table](http://www.ti.com/ww/en/wireless_connectivity/sensortag/tearDown.html) - and the TI SensorTag phone application explore what other sensors are available... feel free to update the gateway config to enable them (hint - this will likely to have an impact on your decoder function) 

### Bugs Happen!
> Note: the Gateway SDK has been compiled with support for Node.js modules. Let's try to run the `nodejs_simple_sample` demo [here](https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/nodejs_simple_sample/README.md#linux-1) first. We recommend you create a new device for it. Due to a [bug](https://github.com/Azure/azure-iot-gateway-sdk/issues/226) the demo is likely to crash. Let's find a way around it.
- Let's fix it by replacing the `iothub_writer.js` module written in Node.js with a native device mapper and a native IoT Hub writer (used in the BLE demo). Edit the `gateway_sample_lin.json` file but don't forget to adjust the paths when adding new modules. Generate a random MAC address for the simulated device. 

### Combine Node.js Simulator With the SensorTag  
- Add your SensorTag as a second device to the `gateway_sample_lin.json` file.

Have Fun!
