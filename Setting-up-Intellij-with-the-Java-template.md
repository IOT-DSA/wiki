## Prerequisites

- A currently working Intellij environment (a fresh install is enough with Gradle enabled)

## Installation instructions

- Go ahead and clone the template repository from https://github.com/IOT-DSA/template-dslink-java.
- Navigate into the root of the template and build it. Building it simply ensures that everything is all set. The created build directory will also be used in the runtime configuration to prevent repository pollution.
   - On Windows (Powershell) use `.\gradlew.bat build`
   - On Unix use `./gradlew build`
- In Intellij, create the project
  - File > Create Project From Existing Sources
  - Open the root directory of the template
  - Use the default Gradle wrapper
  - Click "Ok"
- Setup the Run Configuration
  - Run > Edit Configurations
  - Create a new configuration for a Java application
  - Rename the configuration
  - Set the main class to `org.dsa.iot.template.Main`
  - Set the program arguments to `-b http://localhost:8080/conn -d ../dslink.json`
    - Configure the URL to your running broker as necessary
  - Append `/build` or `\build` (depending on your system platform) to the working directory
  - Save your configuration

That's it! Run your DSLink and it will be connected to your broker.