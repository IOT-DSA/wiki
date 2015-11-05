## Warning

For the simple fact that you are reading this page, your intelligence and sanity are both being questioned. The content below contains a fair amount of sarcasm and resent towards Eclipse.

## Prerequisites

- Java development experience
- Some intelligence
- The personality of a masochist (Eclipse is quite reliable at causing pain)

## Installation Instructions
This page is for people who just don't seem to know how to use Google or prefer a mediocre development environment. Only awesome people use https://www.jetbrains.com/idea.

The first thing that returns from searching for "Gradle for Eclipse" is this link: https://marketplace.eclipse.org/content/gradle-integration-eclipse-0
Make sure to click on the link and follow the installation instructions. If you don't know how to use the "Install" button, well, you have some problems to deal with.

Make sure you clone the template repository from https://github.com/IOT-DSA/template-dslink-java.
If you don't have git installed, click the "Download Zip" button.

Import the project into Eclipse:
File > Import Project > Gradle Project > Path to the template

Setup a run configuration:

- Set the working directory to template/build
- Set the program arguments to: -b http://localhost:8080/conn -d ../dslink.json

Run it. Congratulations, you're running an editor slightly better than Microsoft Notepad!