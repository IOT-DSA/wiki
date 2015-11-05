## Prerequisites

- A currently working Eclipse environment (a fresh install is enough)

## Installation instructions

- Navigate to https://marketplace.eclipse.org/content/gradle-integration-eclipse-0
- Use the "Install" button to install the Gradle integration for Eclipse.
- Once Gradle is installed, go ahead and clone the template repository from https://github.com/IOT-DSA/template-dslink-java.
  - Navigate into the root of the template and build it. Building it simply ensures that everything is all set. The created build directory will also be used in the runtime configuration to prevent repository pollution.
     - On Windows (Powershell) use `.\gradlew.bat build`
     - On Unix use `./gradlew build`
- In Eclipse, import the project
  - File > Import Project > Gradle > Gradle Project
  - Set the "Project Root Directory" to the root directory of the template
  - Click "Finish"
- Setup the Run Configuration
  - Run > Run Configurations
  - Create a new configuration for a Java application
  - Set the main class to `org.dsa.iot.template.Main`
  - Set the program arguments to `-b http://localhost:8080/conn -d ../dslink.json`
    - Configure the URL to your running broker as necessary
  - Set the working directory to `${workspace_loc:template-dslink-java}/build`
  - Save your configuration

That's it! Run your DSLink and it will be connected to your broker.