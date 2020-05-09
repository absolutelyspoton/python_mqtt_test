# python_mqtt_test

Client Python application to talk to an MQTT Broker

Author: Dominic Williams
Last Updated : 9th May 2020 ( year of the great lcokdown )
Favourite Film : Not seen any this year !!!

Notes:

I decided I wanted to setup a time series store using KDB+. In order to populate this store I asked a good friend of mine
where to get a source of time series ticks / events. He mentioned in his line of work ( mentioning no names ), he publishes
data from hardware devices ( mention no names ), onto a MQTT message broker for his clients to pick up. I thought, ok, how
do I do that in Python. Turns out mostly all the hard waor is done of course, I just need a Python Class Library thant can
handle the connectivity and communication protocol. A quick seach on the web and tra la ! The Paho Python Client

The Paho Python Client provides a client class with support for both MQTT v3.1 and v3.1.1 on Python 2.7 or 3.x. 
It also provides some helper functions to make publishing one off messages to an MQTT server very straightforward.

https://www.eclipse.org/paho/clients/python/

This little readme file is not the KDB+ solution. It's a brief narrative of the steps taken to get this up and running.

Did i mention this is my first real project on my Rasberry Pi ?

1. Prerequisites

Raspberry PI (4)
Raspian Linux - (Debian 4.19.97-v7l+)
Python 3.7

Oh and of course the Paho Python Client

2. Approach

Firstly i needed an IDE and a github account to start my project. I am a huge funa of Visual Studio Code and was relieved to 
find that (it being open sourced), there is a version for the Raspberry Pi

https://code.visualstudio.com/

Jay or aka Headmelted provides builds of Visual Studio Code that can work on the less popular platforms. 
This range includes operating systems such as ChromeOS, Raspbian, Linux Mint, Fedora and more.

I followed the following instructions and this just worked

https://pimylifeup.com/raspberry-pi-visual-studio-code/

VS COde works just fine ( with a few little annoying UI glitches but nothing major ). Of course in needed a lint for Python
so I just went for the Microsoft plugin. This wortks fine and I only need it for basic purposes.

I created a project in Github, i configured my global git congig to recognise me ( user..name and user.email ), and then I 
also set a credentials cache ( not secure ), but just to get me automatically authenticating to githib every time i pull or push

Then I started by taking a look at the examples provided on gitub for the paho client

https://github.com/eclipse/paho.mqtt.python/tree/master/examples

All I needed for the time being was a simple subscriber ( listener ), my pal was publishing onto an MQTT message broker

After some back and forth I eventually managed to consume messages published onto the broker and topic

Et Voila ( subscribe.py in short below )

    import paho.mqtt.client as mqtt


    def on_connect(mqttc, obj, flags, rc):
        print("rc: " + str(rc))


    def on_message(mqttc, obj, msg):
        print(msg.topic + " " + str(msg.qos) + " " + str(msg.payload))


    def on_publish(mqttc, obj, mid):
        print("mid: " + str(mid))


    def on_subscribe(mqttc, obj, mid, granted_qos):
        print("Subscribed: " + str(mid) + " " + str(granted_qos))


    def on_log(mqttc, obj, level, string):
        print(string)

    mqttc = mqtt.Client()
    mqttc.on_message = on_message
    mqttc.on_connect = on_connect
    mqttc.on_publish = on_publish
    mqttc.on_subscribe = on_subscribe

    mqttc.connect("broker.hivemq.com", 1883, 60)
    mqttc.subscribe("EXS82Demo")

    mqttc.loop_forever()