# kafka-notes
<iframe data="kafkaNotes.pdf" type="application/pdf" width="700px" height="700px">
</iframe>  

[Theory](kafkaNotes.pdf)

## Producer

- ```producer.send(producerRecord);``` Here the send data is an async activity so we have to flush it before we close. ```producer.flush(); producer.close();```

## Consumer
- **auto.offset.reset** can have 3 values => none(If the consumer group is not set then it will throw error), earliest(read from the beginning of the topic), latest(read the messages that are just sent) ```properties.setProperty("auto.offset.reset","earliest");```
- Whenever consumers join/leave a group partitions are assigned/removed from them. Moving partitions between consumers is known as **rebalance**. There are two types of rebalance
  - **Eager rebalance** : This is the default settings. Whenever a consumer joins/leaves a group all the consumers lose connection with the assigned partitions and new partition assignment takes place. Thus for a short duration the entire consumer group has stopped processing messages. This is known as the stop the world event.
  - **Cooperative rebalance**(Incremental rebalance) : Here instead of reassigning all partitions to all consumers here a small subset of partitions is reassigned from one consumer to another. Thus stop the world event doesnt take place here.
- The Kafka Consumer has a property called "partition.assignment.strategy". This takes in 3 values:
  - **RangeAssignor**: Assigns partitions on a per topic basis. This can lead to imbalance. Eager rebalance
  - **RoundRobin**: Assigns partitions across all topics in round robin fasion. Promotes optimal balance. Eager rebalance
  - **StickyAssignor**: Balanced like RoundRobin, and then minimises partition movements when consumers join/leave the group. Eager rebalance
  - **CooperativeStickyAssignor**: Identical to StickyAssignor but supports Cooperative rebalance
  - By default **[RangeAssignor, CooperativeStickyAssignor]** is assigned. By default RangeAssignor will be used. If RangeAssignor is removed from the array then CooperativeStickyAssignor will be used. In kafka connect, Cooperative rebalance is the default. In kafka streams Cooperative rebalance is turned on by defaultby using the StreamsPartitionAssignor.
- **Static group membership**: When a consumer goes down the partition assigned to it will not be assigned to another partition immediately instead it will wait until the session.timeout.ms is over. This is helpful when consumers maintain local state. If group.instance.id is assigned to a consumer then it becomes a static member.
- If "enable.auto.commit=true" and "auto.commit.interval.ms=5000" then everytime the consumer polls, the consumer checks if the time has passed above 5 secs then the offsets will be committed asynchronously.
- Code should be written to handle cases like sudden and abrupt broker shut downs. In such a case the consumers have to be shut down gracefully(ie the offsets hould be committed). 







