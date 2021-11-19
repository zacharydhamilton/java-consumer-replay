sh ~/kafka/bin/kafka-consumer-groups.sh --bootstrap-server pkc-lzvrd.us-west4.gcp.confluent.cloud:9092 --command-config setup.properties --group consumer-replay --topic replay-topic --reset-offsets --to-offset 100 --execute

sh ~/kafka/bin/kafka-consumer-groups.sh --bootstrap-server pkc-lzvrd.us-west4.gcp.confluent.cloud:9092 --command-config setup.properties --group consumer-replay --topic replay-topic --reset-offsets --to-datetime 2021-11-17T15:55:37.516 --execute

sh ~/kafka/bin/kafka-consumer-groups.sh --bootstrap-server pkc-lzvrd.us-west4.gcp.confluent.cloud:9092 --command-config setup.properties --group consumer-replay --topic replay-topic --reset-offsets --to-earliest --execute