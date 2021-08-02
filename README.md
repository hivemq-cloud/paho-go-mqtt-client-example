# Paho Go MQTT Client Example

## Overview
This is an MQTT client example project that showcases how you can use HiveMQ Cloud with the Eclipse Paho Go Client. The example project covers the basic MQTT functionality: Connecting MQTT clients to your HiveMQ Cloud cluster, subscribing to topics and publishing data (sending and receiving messages using the MQTT protocol).

The Eclipse Paho project provides open source, mainly client side, implementations of MQTT and MQTT-SN in a variety of programming languages.
The Go client used here supports MQTT v3.1 and v3.1.1.
You can find documentation to this client library at https://www.hivemq.com/blog/mqtt-client-library-encyclopedia-golang/.

This example repository is easily and clearly structured, so you can start quickly:
This readme file is your starting point. Here we will describe what you have to do step-by-step to get started with this example.
[``example.go``](example.go) is a simple implementation that demonstrates the core functionality of an MQTT Client.
The files [``go.mod``](go.mod) and [``go.sum``](go.sum) are necessary for building the repository correctly.
Follow the instructions in the following paragraphs to get started yourself.

## HiveMQ Cloud
[HiveMQ](https://www.hivemq.com/) is the industry leader for enterprise-ready, beautifully scalable, large-scale IoT deployments with MQTT. We help companies connect things to the Internet. Our MQTT-based messaging platform ensures fast, reliable, and secure movement of data to and from connected IoT devices for companies around the world. HiveMQ Cloud is our fully-managed, cloud-native IoT messaging platform that makes trustworthy and scalable IoT device connectivity simple. You can learn more about HiveMQ Cloud on our [website](https://www.hivemq.com/mqtt-cloud-broker/), [documentation](https://www.hivemq.com/docs/hivemq-cloud/introduction.html)  and our [blog posts](https://www.hivemq.com/tags/cloud/).

## Getting started
[By signing up](https://console.hivemq.cloud) for HiveMQ Cloud you will automatically get access to a HiveMQ Cloud Basic cluster. HiveMQ Cloud Basic is our smallest package that allows you to connect up to 100 MQTT clients for free and test the full MQTT functionality. 

The [HiveMQ Cloud Quick Start Guide](https://www.hivemq.com/docs/hivemq-cloud/introduction.html#guide) gives you step-by-step instructions on how to set up your HiveMQ Cloud account, create clusters, and connect MQTT clients.

### Prerequisites 
After signing up, you have a running HiveMQ Cloud cluster, that you can use in this example.
Now clone this repository into your local IDE.

### Broker credentials
To define the HiveMQ Cloud cluster which should be targeted, you need to fill the placeholders in the code with your host name, username and password.
The <b>host name</b> can be found in the <b>Details</b> section of the <b>Overview</b> tab of your cluster.
![cluster overview](/img/hivemq-cloud-cluster-overview.png)

After the cluster is created, add a set of credentials that you can use in this example or future implementations.
Use any secure username and password you desire.
The <b>username</b> and <b>password</b> are the values used as <b>Client Credentials</b> that you just created.
![credentials](/img/hivemq-cloud-credentials.png)

## Code Examples
This example project covers the core functionality of an MQTT client interacting with HiveMQ Cloud.
To securely connect the MQTT client with HiveMQ Cloud you need to enable TLS, which is done with ``tls://`` in the serverURI.
Use your username and password, to authenticate your MQTT client at HiveMQ Cloud.
To connect the client, use the port 8883 that is standard for secure MQTT communication. 

```go
var broker = "<host_name>"
var port = 8883
opts := mqtt.NewClientOptions()
opts.AddBroker(fmt.Sprintf("tls://%s:%d", broker, port))
opts.SetClientID("<client_name>")
opts.SetUsername("<username>")
opts.SetPassword("<password>")
```

The code located inside [``example.go``](example.go) connects to the configured HiveMQ Cloud Broker in a simple way. 
This is a ready-set example that can simply be run with ``go run example.go`` after inputting your credentials.

After creating the client and connecting, the code first subscribes to the topic filter "topic/test" with the function ``subscribe(client mqtt.Client)``.  
That means the MQTT client receives all messages that are published to this [topic filter](https://www.hivemq.com/blog/mqtt-essentials-part-5-mqtt-topics-best-practices/).
If it subscribes successfully, a confirmation message is printed, otherwise an error message is printed. 
```go
func subscribe(client mqtt.Client) {
	// subscribe to the same topic, that was published to, to receive the messages
	topic := "topic/test"
	token := client.Subscribe(topic, 1, nil)
	token.Wait()
	// Check for errors during subscribe 
	if token.Error() != nil {
		fmt.Printf("Failed to subscribe to topic")
		panic(token.Error())
	}
	fmt.Printf("Subscribed to topic: %s", topic)
}
```
Then the code publishes 10 messages in a for loop and prints them to the terminal with the function ``publish(client mqtt.Client)``.
```go
func publish(client mqtt.Client) {
	// publish the message "Message" to the topic "topic/test" 10 times in a for loop
	num := 10
	for i := 0; i < num; i++ {
		text := fmt.Sprintf("Message %d", i)
		token := client.Publish("topic/test", 0, false, text)
		token.Wait()
		// Check for errors during publishing
		if token.Error() != nil {
			fmt.Printf("Failed to publish to topic")
			panic(token.Error())
		}
		time.Sleep(time.Second)
	}
}
```
When the messages are received, they are printed by the ``messagePubHandler``.

## Learn more

If you want to learn more about MQTT, visit the [MQTT Essentials](https://www.hivemq.com/mqtt-essentials/) guide, that explains the core of MQTT concepts, its features and other essential information. To learn more about the Go programming language, visit their [official website](https://golang.org/). For more information about how MQTT works in Go, visit the [Github repository](https://github.com/eclipse/paho.mqtt.golang) of the eclipse foundation for paho.mqtt.golang. You can also find extensive documentation there.

## Contributing

Please see our [contributing guidelines](./CONTRIBUTING.adoc) and [code of conduct](./code-of-conduct.md).

## License

[Apache 2.0](./LICENSE).

