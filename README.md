# Java Consumer Replay

This repo contains examples of "replaying" the consumption of previously committed offsets for a given consumer group. These examples use the `kafka-consumer-groups.sh` tool. There are other ways to reset committed offsets for consumer groups, but the examples in this repo will focus on demonstrating this tool in particular. 

## Prerequisites

These examples require a few prerequisites. See below:
1. Confluent Cloud account with a few provisioned resources.
    - A Kafka Cluster, either basic (recommended), standard, or dedicated.
    - A single partition topic named `replay-topic`.
    - A API Key/secret pair for use with the Java Consumer client and the `kafka-consumer-groups.sh` tool.
    
1. Docker.
1. IntelliJ.

## Getting Started

1. Clone this repo.
    ```shell
    git clone https://github.com/zacharydhamilton/java-consumer-replay.git
    ```

1. Open `java-consumer-replay/` with IntelliJ.
1. Build the project with the Gradle tools.
1. Open the file `setup.properties` and change the placeholder values of `<bootstrap-servers>`, `<client-api-key>`, `<client-api-secret>` with the bootstrap server address of your cluster, and your API Keys.
1. Open `Producer.java` and run the main class. This will produce 250 messages to the topic `replay-topic` at a rate of 1 message/second. Wait for this to complete before moving on. 
1. Open `Consumer.java` and run the main class. This will consume the 250 messages that were produced to the topic `replay-topic` and print them to the console. Once all the messages have been consumed, the consumer will hang waiting for new messages (which will never come). 
1. Stop the consumer. 
   > **Note:** You cannot reset the offsets for a consumer group which is in an "active" state. Stopping the consumer will allow you to reset the offsets in a later step.

## Resetting the Consumer Group's Offsets

1. Pull the base image `confluentinc/cp-server`.
    ```shell
    docker pull confluentinc/cp-server
    ```
1. Ensure that you're within the project directory `java-consumer-replay/`, then run the container.
    ```shell
    docker run -it --rm \
        -v $(pwd)/setup.properties:/home/appuser/setup.properties:ro \
        -exec confluentinc/cp-server /bin/sh
    ```
1. Using the following command (replacing `<bootstrap-server>` with the value from your own cluster), reset the offsets for the consumer group to offset 100.
    ```shell
    sh /bin/kafka-consumer-groups.sh --bootstrap-server <bootstrap-servers> --command-config setup.properties \ 
      --group consumer-replay --topic replay-topic \
      --reset-offsets --to-offset 100 --execute
    ```
    The command will output the new offset position for the consumer group.

1. Start the consumer again by running the main class of `Customer.java` in IntelliJ. This will print the messages from the topic a second time, starting with the message with offset 100. 

1. Stop the consumer after the messages have all been printed to the console (last message should have offset 249).

1. Similarly to above, use the following command to reset the offsets for the consumer group, this time setting the offsets using a timestamp instead (copy-paste one of the timestamps from the previously console printed messages, replacing `<timestamp>`).
    ```shell
    sh /bin/kafka-consumer-groups.sh --bootstrap-server <bootstrap-servers> --command-config setup.properties \
      --group consumer-replay --topic replay-topic \
      --reset-offsets --to-datetime <timestamp> --execute
    ```
   Like last time, the command will output the new offset position, this time corresponding to the timestamp chosen.

1. Start the consumer a third time by running the main class of `Consumer.java` in IntelliJ. This will print the messages from the topic a third time, starting with the earliest message subsequent to the timestamp you provided. 

1. Stop the consumer after the messages have all been consumed like above. 

1. Again, use the following command to reset the offsets for the consumer group, this time setting the offsets to the earliest available offset for the topic.
    ```shell
    sh /bin/kafka-consumer-groups.sh --bootstrap-server <bootstrap-servers> --command-config setup.properties \
      --group consumer-replay --topic replay-topic \
      --reset-offsets --to-earliest --execute
    ```
    Again, this command will output the new offset position. This will be whatever the earliest offset for the topic is.    

1. Start the consumer a fourth and final time by running the main class of `Consumer.java` in IntelliJ. This will print the messages from the topic a fourth time, starting with the earliest offset in the topic.

1. Stop the consumer after all the messages have been consumed for the final time. 

## Cleanup

Since these examples leveraged resources that cost money, be sure to shut down the cluster you used if you launched the cluster solely for the purpose of these examples.