# streaming-conf-sb
Live configuration of Spark Streaming applications using Azure Service Bus sample project.

# Notes
* As a prerequisite to running this project, you'll need to have already created an Azure Service Bus queue within your Azure subscription.
* The sample project's ExampleStream is based on a Spark QueueStream which does not support checkpointing. Consequently, recovering the demo application from a checkpoint will fail. Checkpointing is included to demonstrate how the approach taken here can be correctly integrated in a production scenario in which checkpointing is enabled. Before running the demo, ensure the provided checkpoint folder is emptied.
* Since the Service Bus thread synchronously passes Configs to Spark, each Config in the Service Bus queue will be used for the configuration of at least one RDD, even if a newer one exists subsequently in the queue. For some applications, it may be desirable to skip older Configs which are waiting in the queue until no newer ones are present. In this case, it should be possible to manually poll additional Configs from the queue within the onMessageAsync callback until no more can be found, passing only the latest one to Spark.
* This sample project uses the Service Bus client library's QueueClient to take advantage of the library's built-in message pump loop / thread pool. However, using this client does not necessarily guarantee that messages will be delivered in the order in which they are submitted to the Service Bus queue (though for our human/UI-triggered use-cases, ordering appeared to be temporally sequential). For applications which require ordering guarantees, enable [Message Sessions](http://docs.microsoft.com/en-us/azure/service-bus-messaging/message-sessions) for the queue. The drawback to this approach is that the QueueClient message loop cannot be used, and no equivalent exists today within the Service Bus Java client library (though [an issue captures this feature request on GitHub](http://github.com/Azure/azure-service-bus-java/issues/121)).
