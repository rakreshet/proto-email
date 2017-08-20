Akka Hands-on course
====================

Installation:
-------------
0. Setup Traiana VPN connection (need access to Stash and Artifactory).
1. Clone this repo.
2. Install IntelliJ (community edition is fine).
3. Install IntelliJ Scala plugin.
4. Install IntelliJ Gradle plugin.
5. Open the main build.gradle file.
6. Run the generateProto Gradle task on the project (from command line or the Gradle tool window).
    - This may fail if you don't have Python 2.7 installed
7. Build the project. IntelliJ might prompt you to select a Scala SDK. If it does, select the option to get it from
 Maven, and download Scala 2.12.1.
8. Run `HelloAkkaServer` and `HelloAkkaClient`. This won't do much, but getting this far means we have successfully
   downloaded the entire Internet and don't have to worry about WiFi acting up...
9. Install Docker.
10. In a terminal, execute the following:
    1. `docker pull cassandra:3.9`
    2. `docker pull wurstmeister/zookeeper:3.4.6`
    3. `docker pull wurstmeister/kafka:0.10.1.0`
    4. `docker-compose up` (in the project's root directory)
11. (Optional) Install IntelliJ Docker plugin.