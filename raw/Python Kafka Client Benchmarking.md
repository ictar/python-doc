原文：[Python Kafka Client Benchmarking](http://activisiongamescience.github.io/2016/06/15/Kafka-Client-Benchmarking/)

---

[Kafka](http://kafka.apache.org/) is an incredibly powerful service that can
help you process huge streams of data. It is written in Scala and has been
undergoing lots of changes. Historically, the JVM clients have been better
supported then those in the Python ecosystem. However, this doesn't need to be
case! The Kafka binary protocol has largely solidified and many people in open
source community are working to provide first class support for non-JVM
languages.

In this post we will benchmark the three main Python Kafka clients.
[pykafka](https://github.com/Parsely/pykafka), [python-
kafka](https://github.com/dpkp/kafka-python) and the newest arrival
[confluent-kafka-client](https://github.com/confluentinc/confluent-kafka-
python). This will also be useful as an introduction to the different clients
in the python ecosystem and their high level APIs.

# Setup and Caveat¶

We are going to keep the setup simple and run a single broker that will auto-
create topics with one partition. Please note this is a contrived setup. Any
production deployment would be multi-broker and likely more partitions, but
for simplicity sake we are going to use one.

### The Plan¶

  * Spin up a single Kafka 0.9 broker

  * We are going to produce and consume 1 million 100 bytes messages with each client.

  * We will require a producer `acks` of 1. meaning that only the leader (we one have one broker anyways) needs to ack a message. Increasing this will ensure your data is not lost due to broker failure but will slow down production.

I am using the following versions:

pykafka 2.3.1

python-kafka 1.1.1

confluent-kafka-python 0.9.1

I ran these tests within Vagrant hosted on a MacBook Pro 2.2Ghz i7.

### Caveat¶

Like all benchmarks, take this with a grain of salt. A single broker on a
local machine is hardly a production deployment. All settings are largely left
to their defaults. Also, the amount of file caching broker does really help
the client consumption speed. To help combat this we will rerun each
consumption test to compensate for caching recently accessed data.

Even with all the normal stipulations, I hope you find this informative.

Installing clients can be complicated by the fact of some C extensions. We are
big fans of [conda](http://conda.pydata.org/docs/) and maintain python3 linux
builds of some of these clients
[here](https://anaconda.org/ActivisionGameScience) with recipes
[here](https://github.com/ActivisionGameScience/ags_conda_recipes).

The easiest way is to use conda to install the clients:

```python

     conda create -n kafka-benchmark python=3 ipython jupyter pandas seaborn -y
     source activate kafka-benchmark
     conda install -c activisiongamescience confluent-kafka pykafka -y # will also get librdkafka
     pip install kafka-python # pure python version is easy to install
    
    
```

If you would like to run this notebook please find the full repo
[here](https://github.com/ActivisionGameScience/python-kafka-benchmark)

Included in this repo is a [docker-
compose](https://github.com/ActivisionGameScience/python-kafka-
benchmark/blob/master/docker-compose.yml) file that will spin up a single
kafka 0.9 broker and zookeeper instance locally. We can shell out and start it
with docker compose.

```python

    !docker-compose up -d
    
```

```python

    Creating network "pythonkafkabenchmark_default" with the default driver
    Creating pythonkafkabenchmark_zookeeper_1
    Creating pythonkafkabenchmark_kafka_1
    
```

```python

    msg_count = 1000000
    msg_size = 100
    msg_payload = ('kafkatest' * 20).encode()[:msg_size]
    print(msg_payload)
    print(len(msg_payload))
    
```

```python

    b'kafkatestkafkatestkafkatestkafkatestkafkatestkafkatestkafkatestkafkatestkafkatestkafkatestkafkatestk'
    100
    
```

```python

    bootstrap_servers = 'localhost:9092' # change if your brokers live else where
    
```

```python

    import time
    
    producer_timings = {}
    consumer_timings = {}
    
```

```python

    def calculate_thoughput(timing, n_messages=1000000, msg_size=100):
        print("Processed {0} messsages in {1:.2f} seconds".format(n_messages, timing))
        print("{0:.2f} MB/s".format((msg_size * n_messages) / timing / (1024*1024)))
        print("{0:.2f} Msgs/s".format(n_messages / timing))
    
```

# [pykafka](https://github.com/Parsely/pykafka)¶

The first client we will examine is pykafka. It the client we have the most
experience with and have used with success. It tries less hard to replicate
the existing java client API. It also has a partition balancing code for kafka
broker version 0.8.2

```python

    from pykafka import KafkaClient
    
    def pykafka_producer_performance(use_rdkafka=False):
        
        # Setup client
        client = KafkaClient(hosts=bootstrap_servers)
        topic = client.topics[b'pykafka-test-topic']
        producer = topic.get_producer(use_rdkafka=use_rdkafka)
    
        msgs_produced = 0
        produce_start = time.time()
        for i in range(msg_count):
            # Start producing
            producer.produce(msg_payload)
                         
        producer.stop() # Will flush background queue
     
        return time.time() - produce_start
    
```

```python

    producer_timings['pykafka_producer'] = pykafka_producer_performance()
    calculate_thoughput(producer_timings['pykafka_producer'])
    
```

```python

    Processed 1000000 messsages in 57.32 seconds
    1.66 MB/s
    17446.37 Msgs/s
    
```

If you are monitoring this function, you will notice that the produce loop
completes and then the function stalls at the `produce.stop()`. This is
because the producer is asynchronous and batches produce calls to Kafka.
Kafka's speed comes from the ability to batch many message together. To take
advantage of this, the client will keep a buffer of messages in the background
and batch them. So, when you call `producer.produce` you are performing no
external I/O. That message is queued in an in-memory buffer and the method
returns immediately. So we are able to load the in-memory buffer faster then
pykafka can send them to kafka. `producer.stop()` will block until all
messages are sent.

So when producing messages make sure you allow the producer to flush the
remaining messages before you exit.

Another way to ensure that the messages where produced is to check the topic
offsets.

```python

    client = KafkaClient(hosts=bootstrap_servers)
    topic = client.topics[b'pykafka-test-topic']
    print(topic.earliest_available_offsets())
    print(topic.latest_available_offsets())
    
```

```python

    {0: OffsetPartitionResponse(offset=[0], err=0)}
    {0: OffsetPartitionResponse(offset=[1000000], err=0)}
    
```

Pykafka has an optional producer backend that wraps the
[librdkafka](https://github.com/edenhill/librdkafka) package. librdkafka is a
pure C kafka client and holds very impressive benchmarks. Let rerun our
pykafka producer test with rdkafka enabled.

```python

    producer_timings['pykafka_producer_rdkafka'] = pykafka_producer_performance(use_rdkafka=True)
    calculate_thoughput(producer_timings['pykafka_producer_rdkafka'])
    
```

```python

    Processed 1000000 messsages in 15.72 seconds
    6.06 MB/s
    63595.38 Msgs/s
    
```

```python

    def pykafka_consumer_performance(use_rdkafka=False):
        # Setup client
        client = KafkaClient(hosts=bootstrap_servers)
        topic = client.topics[b'pykafka-test-topic']
    
        msg_consumed_count = 0
        
        consumer_start = time.time()
        # Consumer starts polling messages in background thread, need to start timer here
        consumer = topic.get_simple_consumer(use_rdkafka=use_rdkafka)
    
        while True:
            msg = consumer.consume()
            if msg:
                msg_consumed_count += 1
    
            if msg_consumed_count >= msg_count:
                break
                            
        consumer_timing = time.time() - consumer_start
        consumer.stop()    
        return consumer_timing
    
```

```python

    _ = pykafka_consumer_performance(use_rdkafka=False)
    consumer_timings['pykafka_consumer'] = pykafka_consumer_performance(use_rdkafka=False)
    calculate_thoughput(consumer_timings['pykafka_consumer'])
    
```

```python

    Processed 1000000 messsages in 29.43 seconds
    3.24 MB/s
    33976.94 Msgs/s
    
```

```python

    # run it once thorough to warm the cache
    _ = pykafka_consumer_performance(use_rdkafka=True)
    consumer_timings['pykafka_consumer_rdkafka'] = pykafka_consumer_performance(use_rdkafka=True)
    calculate_thoughput(consumer_timings['pykafka_consumer_rdkafka'])
    
```

```python

    Processed 1000000 messsages in 6.09 seconds
    15.67 MB/s
    164311.50 Msgs/s
    
```

# [kafka-python](https://github.com/dpkp/kafka-python)¶

kafka-python aims to replicate the java client api exactly. This is a key
difference with pykafka, which trys to maintains "pythonic" api. In earlier
versions of kafka, partition balancing was left to the client. Pykafka was the
only python client to implement this feature. However, with kafka 0.9 the
broker provides this, so the lack of support within kafka-python is less
important.

```python

    from kafka import KafkaProducer
    
    def python_kafka_producer_performance():
        producer = KafkaProducer(bootstrap_servers=bootstrap_servers)
    
        producer_start = time.time()
        topic = 'python-kafka-topic'
        for i in range(msg_count):
            producer.send(topic, msg_payload)
            
        producer.flush() # clear all local buffers and produce pending messages
            
        return time.time() - producer_start
    
```

```python

    producer_timings['python_kafka_producer'] = python_kafka_producer_performance()
    calculate_thoughput(producer_timings['python_kafka_producer'])
    
```

```python

    Processed 1000000 messsages in 67.86 seconds
    1.41 MB/s
    14737.12 Msgs/s
    
```

```python

    from kafka import KafkaConsumer
    
    def python_kafka_consumer_performance():
        topic = 'python-kafka-topic'
    
        consumer = KafkaConsumer(
            bootstrap_servers=bootstrap_servers,
            auto_offset_reset = 'earliest', # start at earliest topic
            group_id = None # do no offest commit
        )
        msg_consumed_count = 0
                
        consumer_start = time.time()
        consumer.subscribe([topic])
        for msg in consumer:
            msg_consumed_count += 1
            
            if msg_consumed_count >= msg_count:
                break
                        
        consumer_timing = time.time() - consumer_start
        consumer.close()    
        return consumer_timing
    
```

```python

    _ = python_kafka_consumer_performance()
    consumer_timings['python_kafka_consumer'] = python_kafka_consumer_performance()
    calculate_thoughput(consumer_timings['python_kafka_consumer'])
    
```

```python

    Processed 1000000 messsages in 26.55 seconds
    3.59 MB/s
    37667.97 Msgs/s
    
```

# [confluent-kafka-python](https://github.com/confluentinc/confluent-kafka-
python)¶

With the latest release of the Confluent platform, there is a new python
client on the scene. confluent-kafka-python is a python wrapper around
librdkafka and is largely built by the same author. The underlying library is
basis for most non-JVM clients out there. We have already mentioned it earlier
when looking at pykafka.

```python

    import confluent_kafka
    topic = 'confluent-kafka-topic'
    
    def confluent_kafka_producer_performance():
        
        topic = 'confluent-kafka-topic'
        conf = {'bootstrap.servers': bootstrap_servers}
        producer = confluent_kafka.Producer(**conf)
        messages_to_retry = 0
    
        producer_start = time.time()
        for i in range(msg_count):
            try:
                producer.produce(topic, value=msg_payload)      
            except BufferError as e:
                messages_to_retry += 1
    
        # hacky retry messages that over filled the local buffer
        for i in range(messages_to_retry):
            producer.poll(0)
            try:
                producer.produce(topic, value=msg_payload)
            except BufferError as e:
                producer.poll(0)
                producer.produce(topic, value=msg_payload)
    
        producer.flush()
                
        return time.time() - producer_start
    
```

```python

    producer_timings['confluent_kafka_producer'] = confluent_kafka_producer_performance()
    calculate_thoughput(producer_timings['confluent_kafka_producer'])
    
```

```python

    Processed 1000000 messsages in 5.45 seconds
    17.50 MB/s
    183456.28 Msgs/s
    
```

```python

    client = KafkaClient(hosts=bootstrap_servers)
    topic = client.topics[b'confluent-kafka-topic']
    print(topic.earliest_available_offsets())
    print(topic.latest_available_offsets())
    
```

```python

    {0: OffsetPartitionResponse(offset=[0], err=0)}
    {0: OffsetPartitionResponse(offset=[1000000], err=0)}
    
```

```python

    import confluent_kafka
    import uuid
    
    def confluent_kafka_consumer_performance():
        
        topic = 'confluent-kafka-topic'
        msg_consumed_count = 0
        conf = {'bootstrap.servers': bootstrap_servers,
                'group.id': uuid.uuid1(),
                'session.timeout.ms': 6000,
                'default.topic.config': {
                    'auto.offset.reset': 'earliest'
                }
        }
    
        consumer = confluent_kafka.Consumer(**conf)
    
        consumer_start = time.time()
        # This is the same as pykafka, subscribing to a topic will start a background thread
        consumer.subscribe([topic])
    
        while True:
            msg = consumer.poll(1)
            if msg:
                msg_consumed_count += 1
                             
            if msg_consumed_count >= msg_count:
                break
                        
        consumer_timing = time.time() - consumer_start
        consumer.close()    
        return consumer_timing
    
```

```python

    _ = confluent_kafka_consumer_performance() # Warm cache
    consumer_timings['confluent_kafka_consumer'] = confluent_kafka_consumer_performance()
    calculate_thoughput(consumer_timings['confluent_kafka_consumer'])
    
```

```python

    Processed 1000000 messsages in 3.83 seconds
    24.93 MB/s
    261407.91 Msgs/s
    
```

The confluent_kafka client is crushingly fast. It can consume over 250K
messages a second from a single broker. Note that the raw C client has been
benchmarked at over 3 million messages/sec, so you see how much overhead
python adds. But on the side of developer speed, you don't have to code in C!

# Comparision¶

```python

    import pandas as pd
    import seaborn as sns
    %pylab inline
    
```

```python

    Populating the interactive namespace from numpy and matplotlib
    
```

```python

    consumer_df = pd.DataFrame.from_dict(consumer_timings, orient='index').rename(columns={0: 'time_in_seconds'})
    producer_df = pd.DataFrame.from_dict(producer_timings, orient='index').rename(columns={0: 'time_in_seconds'})
    
```

```python

    consumer_df['MBs/s'] = (len(msg_payload) * msg_count) / consumer_df.time_in_seconds / (1024*1024)
    producer_df['MBs/s'] = (len(msg_payload) * msg_count) / producer_df.time_in_seconds / (1024*1024)
    
    consumer_df['Msgs/s'] = msg_count / consumer_df.time_in_seconds
    producer_df['Msgs/s'] = msg_count / producer_df.time_in_seconds
    
```

```python

    producer_df.sort_index(inplace=True)
    producer_df
    
```

| time_in_seconds | MBs/s | Msgs/s  
---|---|---|---  
confluent_kafka_producer | 5.450890 | 17.495754 | 183456.277455  
pykafka_producer | 57.318527 | 1.663815 | 17446.365994  
pykafka_producer_rdkafka | 15.724413 | 6.064928 | 63595.378094  
python_kafka_producer | 67.855882 | 1.405441 | 14737.115900

```python

    consumer_df.sort_index(inplace=True)
    consumer_df
    
```  
  
| time_in_seconds | MBs/s | Msgs/s  
---|---|---|---  
confluent_kafka_consumer | 3.825439 | 24.929801 | 261407.908007  
pykafka_consumer | 29.431728 | 3.240293 | 33976.938217  
pykafka_consumer_rdkafka | 6.086001 | 15.669966 | 164311.503412  
python_kafka_consumer | 26.547753 | 3.592298 | 37667.971237

```python

    producer_df.plot(kind='bar', subplots=True, figsize=(10, 10), title="Producer Comparison")
    
```

```python

    array([<matplotlib.axes._subplots.AxesSubplot object at 0x7f8ef4e2edd8>,
           <matplotlib.axes._subplots.AxesSubplot object at 0x7f8ef2978940>,
           <matplotlib.axes._subplots.AxesSubplot object at 0x7f8ef293c240>], dtype=object)
```  
  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAmkAAAMBCAYAAAC0oSNRAAAABHNC
SVQICAgIfAhkiAAAAAlwSFlz

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

H+JrX/sXFi5cwJFHvpt3vetQ7r//z9xww0xmzLicxYuf57jj/mZZSPvhD2fwox/9Jy0tLSxcWMbN

DyVJqhdDWgF22mln1llnHdZZZx1aW9t405veDMCUKVswa9b/8swzz/CHP9zDZz/7Kfr6+gBYvHjx

kNc/depbAdhqq9cxe/bsAefbZpvtuPTS6cydO4epU/dm001fxZ133s6f/5x88INH0dfXx3PPPceE

CRP44x/vZccdd2KjjTYCoK2t9oDY3//+bs4889xqu97A008/zaJFtYfev+lNe9LS0sIGG7yc9vYN

6enp5ve/v5u3vGUvxo0bx7hx49hjj7csq2eLLbbk9NM/zVveshdvfvNeQ95eSZLGAkNaAcaNG7fs

dVNTE+PGrQ3AWmutRW9vL319S2hrW5/p03/4ota/9trjXrC+geyzz/5ss8123HLLrzjxxOM58cRT

gT723/8d/N3fffQF8/7617+iyovLaRqkjrWXvW5uXovFiweuBeDccy/g7rt/x803/zeXXjqdSy+9

grXW8q4xkqQ1wxr5/3iL5s1lQc9jw/K3aN7cIX3m+PHjl/Uo9a083Qyy7Hq88pUbc+ONM5e1/e//

3r9a6/g/A3/2448/xsYbb8J73nM4e+45lQce+F923nkXfvnLX9DT0wPA008/zezZs9lmm+245567

mD37iWXtAK9//Y5cd91PAfjd7+5kgw1ezvjx41esovoOdthhR371q5t47rnnWLRoIb/+9a+WzTNn

zmx23HFnPvzhj7Fw4UKeeWbRi9xmSZJGnzWuJ23y5ClccOJBw77OVVl//Q3YbrvXc/TRh/PqV09m

4B6nlbd/7nNf4MtfPosZM6bT27uYt71tX7bYYtXPHGtqWn59A/d03XDDz7nuup/S0tLChhu+gqOO

Oo62tjY++MGP8MlPfpQlS/pYe+21+eQnT2LrrbflpJM+zamn/hN9fX20t0/gvPMu4thjP8iXvvR5

jj76CNZdd10+85kzBq3rta/dir33nsbRRx/OhAkbsvXW2wC107mf//xnWbhwIdDHoYceznrrta5y

eyVJGiuaVrdXZ6RExAbAd4FtgSXAccCfgSuAzYBO4LDMnDfYerq65pexQYXp6Gijq2t+o8vQKOH+

oqFyXynbAw/czynfvtVndy5nQc9jfOlDuxXxgPWOjrYBe09KOt15AfDTzHwd8Hrgf4CTgZmZGcAN

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

)

```python

    consumer_df.plot(kind='bar', subplots=True, figsize=(10, 10), title="Consumer Comparison")
    
```

```python

    array([<matplotlib.axes._subplots.AxesSubplot object at 0x7f8ef286c048>,
           <matplotlib.axes._subplots.AxesSubplot object at 0x7f8ef2855908>,
           <matplotlib.axes._subplots.AxesSubplot object at 0x7f8ef27a4080>], dtype=object)
```

![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAmkAAAMFCAYAAAAvMGFHAAAABHNC
SVQICAgIfAhkiAAAAAlwSFlz

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

JA2o140YwXojex5ZfyAMH74q06Y9zEsvvcSwYcO4/fZbGTNmbL9/zq23/pntt9+x37cLhjRJkrSc

2mGHnfjzn//ILru8nRtuuJ7dd9+Le++9G4C7776Tr33tKzQ0NAANXHzxZQwfPpyvfOVc7rnnTsaM

GUtjYyP77XcAu+zydr7xjYu4+eY/0NjYxHbbbc+HP3wCALfeejPHHvtB/vGPZzj99NOYO/d55s+f

z0knncJWW23zquo3pEmSpOVOQ0MDu+++J5MmXcaOO+7Mww8/xH77HbAwpF155fc46aRT2GKLrXjx

xRdZeeWV+d3vbmTmzOl873tX0d7+Dw4//GD22+8AnntuFn/4w2/5wQ/+B4Dnn69dV7dgwQIef/wx

1l9/HFde+T22335HjjjiGDo7O3nxxRdf9T54d6ckSVoujR+/EdOnP80NN1zPjjvuTPenLG255dZ8

7WsTufrqK5k9+zkaGxv5y1/uYbfddgdg9Og1ecMb3gjAaquNZJVVVuGcc87id7+7iVVWGQ7AAw/c

z+abbwHAZptN4Be/+BlXXHEZf//7Q6y66qqvun5DmiRJWm7tvPPbuOSSC9l9971eMf197zuaU075

LP/85z/58Iffz2OPtfa4jcbGRi67bAq77vof3HzzHzjppI8BcMstNy+8Hm3rrbfl4osvpaWlhS9+

8Qyuv/6Xr7p2uzslSdKAenru3H7d1gZ9WK6r1WzfffenubmZ8eM35O6771w4/8knn2D8+A0ZP35D

HnzwAR577FG23HJrfvWrX7D33vvS0dHO3XffxZ577sMLL7zAiy++yA47vIUtttiKQw89EIA777yN

ww8/CoDp06czZswY9tvvQP75z5f429/+j732eser2ldDmiRJGjDjxo2Hr34NgNGjX/04aRt0bXMp

ajcEQEvLGA466D2Lzb/qqh9y1113sNJKjWywwXh22GEnGhsbufPOOzjiiEMYM2YsEZuy2mojmTv3

eU455SReeuklAD72sRN59tlnGTZs+MJuzbvvvoMf/vC7NDU1MWLEanzmM59/VfsJ0NC9f3Z50NY2

e/naoX7S0tJMW9vsepehIcLjRX3lsaJlMRSOlxdeeIFVV12V556bxQc+cDTf+MbljBo1erHlpk79

FW1tMxe2pP27WlqaG3qaZ0uaJElS5eSTP8GcObOZN28eRx/9/iUGNIA999xnwGsxpA2Q7o/BKEF/

PYqjP6woj/OQJA09F130rXqXsJAhbYB0fwxGCfrjURz9oa+P85AkaUVnSBtA9XgMhiRJWj4sNaRF

xLrAFGAssAC4NDMviojTgeOBmdWip2XmddU6pwLHAvOAEzJzajX9DcB3gOHALzPzE9X0YdVnvBF4

BnhPZj5WzTsK+DTQCZydmVP6Yb8lSZKK1pfBbOcBJ2bmBGBH4KMRsWk1b2JmvqH61xXQNgMOATYD

9gEuiYiuOxe+ARyXmZsAm0RE18hyxwHtmbkxcAFwXrWtUcDngDcD2wOnR8Qar26XJUmSyrfUkJaZ

0zPznur1HOBBYJ1q9pJuGz0AuDIz52VmK/AQsF1ErAU0Z+bt1XJTgAO7rTO5en018Pbq9V7A1Myc

lZnPAlOBvZdh/yRJkoakZXosVESMA7YBbq0mfTQi7omIb3dr4VoHeLzbak9W09YBnug2/Qn+FfYW

rpOZ84FZETG6l21JkiQt1/p840BEjKTWynVCZs6JiEuAMzOzMyK+AHwFeH8/1dXjwG5LM2rUCJqa

6j+8Q0fHyGLuqCzN6NEjaWnxhorS+RuprzxWtCw8XvquTyEtIpqoBbTvZuY1AJnZ1m2Ry4CfVa+f

BF7fbd661bSepndf56mIaARWz8z2iHgS2HWRdW7qrdaOjv57PtirUcqYZCVqb59T/IjTK7qhMCq4

yuCxomXh8bK43kJrX7s7JwEPZOaFXROqa8y6vAu4v3p9LXBoRAyLiA2AjYDbMnM6tW7M7aobCY4E

rum2TtdzFQ4GbqxeXw/sERFrVDcR7FFNkyRJWq71ZQiOnYDDgfsi4m5qQ2GcBhwWEdtQG5ajFfgg

QGY+EBE/Bh4AXgY+nJldz9P8CK8cguO6avrlwHcj4iHgH8Ch1bY6IuIs4I7qcz9f3UAgSZK0XPMB

6wPk4YcfYtqnT3Ew20U8Nmc2G5x9jk8cKJxdEuorjxUtC4+XxfX2gPVlurtTkiRJg8OQJkmSVCBD

miRJUoEMaZL0/9u78yi7qjrt49+QiIgJdKIBlHl8IAytAQMO3S+gNNAo0CI4ICKgLY2vgNouwQGE

VnFYDmC3qDQCidiIioIDGBlEaVQmaZHQT/M2hEkmSQgEFDO9f5xTcFNUpW5lcJ9z8nzWqpV79h14

itr31q/22XufiIgGSpEWERER0UAp0iIiIiIaKEVaRERERAOlSIuIiIhooBRpEREREQ2UIi0iIiKi

gVKkRURERDRQirSIiIiIBkqRFhEREdFAKdIiIiIiGihFWkREREQDpUiLiIiIaKAUaRERERENlCIt

IiIiooHGjfQASRsB04H1gcXAWbbPkDQR+BawKTAbOMT2vPo5JwJHAguB42zPrNunAucCawE/tn18

3b5m/d/YGfgD8Ebbd9f3HQ58GFgCfML29JXynUdEREQ0WD8jaQuB99neHng58G5J2wInAJfbFnAl

cCKApCnAIcB2wL7AlyWNqV/rTOAo29sA20jau24/Cphje2vgi8Bn6teaCJwEvAzYFThZ0ror+D1H

RERENN6IRZrtB2zfXN+eD9wGbAQcAJxXP+w84MD69v7ABbYX2p4N3A5Mk7QBMMH29fXjpvc8p/e1

vgPsWd/eG5hpe57tR4GZwD7L841GREREtMmo5qRJ2gx4CfArYH3bD0JVyAHr1Q/bELin52n31W0b

Avf2tN9bty31HNuLgHmSJi3jtSIiIiI6re8iTdJ4qlGu4+oRtSWDHjL4eEWMGfkhEREREd014sIB

AEnjqAq0GbYvrpsflLS+7QfrU5kP1e33ARv3PH2jum249t7n/F7SWGAd23Mk3QfsPug5Vy0r68SJ

azNu3Nh+vq1Vau7c8dxZOkRDTZo0nsmTJ5SOESPIzyj6lb4So5H+0r++ijTg68As26f3tF0CvB34

NHA4cHFP+/mSvkB1anIr4DrbSyTNkzQNuB54G3BGz3MOB34NHEy1EAHgJ8An6sUCawB7US1YGNbc

uU/2+S2tWnPmzC8dobHmzJnPww8/XjpGLMPkyRPyM4q+pK/EaKS/PNuyitZ+tuB4JXAocIuk31Cd

1vwQVXF2oaQjgbuoVnRie5akC4FZwALgGNsDp0LfzdJbcFxWt58NzJB0O/AI8Kb6teZK+hfghvq/

e0q9gCAiIiKi08YsWbIyp5KV9/DDjzfiG/rf/72dOz98ApuMz7Bur7vnP87mn/gUW265dekosQz5

azf6lb4So5H+8myTJ08Ydh5+rjgQERER0UAp0iIiIiIaKEVaRERERAOlSIuIiIhooBRpEREREQ2U

Ii0iIiKigVKkRURERDRQirSIiIiIBkqRFhEREdFAKdIiIiIiGihFWkREREQDpUiLiIiIaKAUaRER

ERENlCItIiIiooHGlQ4QERH9W7RoEbNn31E6xtPmzh3PnDnzS8cAYLPNtmDs2LGlY0SsNCnSIiJa

ZPbsO7j2vcfyorXXLh0FgDtLB6jd/+ST8IUz2HLLrUtHiVhpUqRFRLTMi9Zem03GTygdIyJWscxJ

i4iIiGigFGkRERERDTTi6U5JZwOvBR60vVPddjLwTuCh+mEfsn1Zfd+JwJHAQuA42zPr9qnAucBa

wI9tH1+3rwlMB3YG/gC80fbd9X2HAx8GlgCfsD19JXzPEREREY3Xz0jaOcDeQ7R/3vbU+mugQNsO

OATYDtgX+LKkMfXjzwSOsr0NsI2kgdc8Cphje2vgi8Bn6teaCJwEvAzYFThZ0rrL801GREREtM2I

RZrta4C5Q9w1Zoi2A4ALbC+0PRu4HZgmaQNggu3r68dNBw7sec559e3vAHvWt/cGZtqeZ/tRYCaw

z8jfUkRERET7rcictP8r6WZJ/94zwrUhcE/PY+6r2zYE7u1pv7duW+o5thcB8yRNWsZrRURERHTe

8m7B8WXgVNtLJH0c+BzwjpWUaagRur5NnLg248aV38xw7tzxjdk/qGkmTRrP5MnZPqDp8jNqpny2

DC+fLe2Qn1H/lqtIs/1wz+FZwA/q2/cBG/fct1HdNlx773N+L2kssI7tOZLuA3Yf9JyrRso2d+6T

/X8jq1BTduBuojlz5vPww4+XjhHLMHnyhPyMGiqfLcPLZ0vz5bPl2ZZVtPZ7unMMPSNc9RyzAa8H

flffvgR4k6Q1JW0ObAVcZ/sBqtOY0+qFBG8DLu55zuH17YOBK+vbPwH2krRuvYhgr7otIiIiovP6

2YLjm1QjWi+QdDdwMrCHpJcAi4HZwLsAbM+SdCEwC1gAHGN7Sf1S72bpLTguq9vPBmZIuh14BHhT

/VpzJf0LcAPVFhyn1AsIIiIiIjpvxCLN9luGaD5nGY8/DThtiPYbgR2HaH+KatuOoV7rXKrCLiIi

ImK1kisORERERDRQirSIiIiIBkqRFhEREdFAKdIiIiIiGihFWkREREQDpUiLiIiIaKAUaREREREN

lCItIiIiooFSpEVEREQ0UIq0iIiIiAZKkRYRERHRQCnSIiIiIhooRVpEREREA6VIi4iIiGigFGkR

ERERDZQiLSIiIqKBUqRFRERENFCKtIiIiIgGGjfSAySdDbwWeND2TnXbROBbwKbAbOAQ2/Pq+04E

jgQWAsfZnlm3TwXOBdYCfmz7+Lp9TWA6sDPwB+CNtu+u7zsc+DCwBPiE7ekr5buOiIiIaLh+RtLO

AUIYH/MAAB88SURBVPYe1HYCcLltAVcCJwJImgIcAmwH7At8WdKY+jlnAkfZ3gbYRtLAax4FzLG9

NfBF4DP1a00ETgJeBuwKnCxp3eX6LiMiIiJaZsQizfY1wNxBzQcA59W3zwMOrG/vD1xge6Ht2cDt

wDRJGwATbF9fP256z3N6X+s7wJ717b2Bmbbn2X4UmAnsM4rvLSIiIqK1lndO2nq2HwSw/QCwXt2+

IXBPz+Puq9s2BO7tab+3blvqObYXAfMkTVrGa0VERER03ohz0vq0ZCW9DsCYkR8yvIkT12bcuLEr

K8tymzt3PHeWDtFQkyaNZ/LkCaVjxAjyM2qmfLYML58t7ZCfUf+Wt0h7UNL6th+sT2U+VLffB2zc

87iN6rbh2nuf83tJY4F1bM+RdB+w+6DnXDVSsLlzn1yOb2flmzNnfukIjTVnznwefvjx0jFiGSZP

npCfUUPls2V4+Wxpvny2PNuyitZ+T3eOYekRrkuAt9e3Dwcu7ml/k6Q1JW0ObAVcV58SnSdpWr2Q

4G2DnnN4fftgqoUIAD8B9pK0br2IYK+6LSIiIqLz+tmC45tUI1ovkHQ3cDLwKeDbko4E7qJa0Ynt

WZIuBGYBC4BjbA+cCn03S2/BcVndfjYwQ9LtwCPAm+rXmivpX4AbqE6nnlIvIIiIiIjovBGLNNtv

Geau1wzz+NOA04ZovxHYcYj2p6iLvCHuO5eqsIuIiIhYreSKAxERERENlCItIiIiooFSpEVEREQ0

UIq0iIiIiAZKkRYRERHRQCnSIiIiIhooRVpEREREA6VIi4iIiGigFGkRERERDZQiLSIiIqKBUqRF

RERENFCKtIiIiIgGSpEWERER0UAp0iIiIiIaKEVaRERERAOlSIuIiIhooBRpEREREQ2UIi0iIiKi

gcatyJMlzQbmAYuBBbanSZoIfAvYFJgNHGJ7Xv34E4EjgYXAcbZn1u1TgXOBtYAf2z6+bl8TmA7s

DPwBeKPtu1ckc0REREQbrOhI2mJgd9svtT2tbjsBuNy2gCuBEwEkTQEOAbYD9gW+LGlM/ZwzgaNs

bwNsI2nvuv0oYI7trYEvAp9ZwbwRERERrbCiRdqYIV7jAOC8+vZ5wIH17f2BC2wvtD0buB2YJmkD

YILt6+vHTe95Tu9rfQd49QrmjYiIiGiFFS3SlgA/lXS9pHfUbevbfhDA9gPAenX7hsA9Pc+9r27b

ELi3p/3eum2p59heBDwqadIKZo6IiIhovBWakwa80vb9kiYDMyWZqnDrNfh4RYwZ+SERERER7bdC

RZrt++t/H5b0fWAa8KCk9W0/WJ/KfKh++H3Axj1P36huG6699zm/lzQWWMf2nGVlmjhxbcaNG7si

39ZKMXfueO4sHaKhJk0az+TJE0rHiBHkZ9RM+WwZXj5b2iE/o/4td5EmaW1gDdvzJT0f+DvgFOAS

4O3Ap4HDgYvrp1wCnC/pC1SnMbcCrrO9RNI8SdOA64G3AWf0POdw4NfAwVQLEZZp7twnl/dbWqnm

zJlfOkJjzZkzn4cffrx0jFiGyZMn5GfUUPlsGV4+W5ovny3PtqyidUVG0tYHvidpSf0659ueKekG

4EJJRwJ3Ua3oxPYsSRcCs4AFwDG2B06Fvpult+C4rG4/G5gh6XbgEeBNK5A3IiIiojWWu0izfSfw

kiHa5wCvGeY5pwGnDdF+I7DjEO1PURd5EREREauTXHEgIiIiooFWdHVnRERENNSiRYuYPfuO0jGe

Nnfu+MbMq9xssy0YO7b8QsNlSZEWERHRUbNn38G17z2WF629dukoAI1ZmXz/k0/CF85gyy23Lh1l

mVKkRTRA/todXhv+2o1oshetvTabjM+2F22UIi2iAfLX7tDa8tduRMSqkCItoiHy125ERPTK6s6I

iIiIBkqRFhEREdFAKdIiIiIiGihFWkREREQDpUiLiIiIaKAUaRERERENlCItIiIiooFSpEVEREQ0

UIq0iIiIiAZKkRYRERHRQCnSIiIiIhooRVpEREREA6VIi4iIiGigcaUD9EPSPsAXqYrKs21/unCk

iIiIiFWq8SNpktYA/hXYG9geeLOkbcumioiIiFi1Gl+kAdOA223fZXsBcAFwQOFMEREREatUG053

bgjc03N8L1Xh1nj3P/lk6QiNc/+TT7J56RANlf7ybOkvQ0tfebb0leGlvzxbW/rLmCVLlpTOsEyS

DgL2tv2P9fFbgWm2jy2bLCIiImLVacPpzvuATXqON6rbIiIiIjqrDac7rwe2krQpcD/wJuDNZSNF

RERErFqNH0mzvQj4v8BM4FbgAtu3lU0VERERsWo1fk5aRERExOqo8SNpEREREaujFGkRERERDZQi

LSIiIqKBUqRFRERENFCKtIiIiFglJI2V9N7SOdoqRVpH1W+Mq0rniHZIf4mIVaHeRit7my6nbMHR

YZKuAF5ve17pLNF86S8xGpK2Bk4DpgBrDbTb3qJYqGgkSV8AngN8C3hioN32TcVCtUQbrjgQy28+

cIukn7L0GyPXPY2hpL/EaJwDnAx8AdgDOIKcnYmhvaT+99SetiXAngWytEqKtG67qP6K6Ef6S4zG

82xfIWmM7buAj0m6ETipdLBoFtt7lM7QVjnd2XGSngdsYtuls0Tzpb9EvyRdC7wK+A5wJXAf8Cnb

KhosGkfS+sAngRfb3lfSFODlts8uHK3xMjTdYZJeB9wMXFYfv0TSJWVTRVOlv8QoHQesDRwL7Awc

BhxeNFE01bnAT4AX18f/AxxfLE2LpEjrto8B04BHAWzfDGRSbwznY6S/RP/+YHu+7XttH2H79cCi

0qGikV5o+0JgMYDthaSv9CVFWrctGGKl3uIiSaIN0l9iNL4jacOBA0l/C3y9YJ5orickvYBqsQCS

dgOyirwPWTjQbbdKegswtl4ufyxwbeFM0VzpLzEaRwPfr0+TT6XajuPvy0aKhnofcAmwpaT/BCYD

bygbqR0yktZt7wG2B54C/gN4jMwDiOGlv0TfbF9PVcjPpDpV/hrb9xQNFY1U74f2f4BXAO8Ctrf9

27Kp2iGrOyMiom+SfkB92qo2BbgfmAtge/8SuaK5JI0F9gM2o+cMnu3Pl8rUFjnd2WGSdgE+xLPf

GDuVyhTNlf4Sffo34I+lQ0Sr/AD4E3ALmec6KinSuu184APkjRH9SX+JfnzS9lRJM2wfVjpMtMJG

+WNv+aRI67aHbWefq+hX+kv0Y816gckrJL1+8J22c9WKGOxSSX9ne2bpIG2TIq3bTpb078AVVJPB

gXyIxrDSX6IfRwOHAn8FvG7QfUvIpcXi2X4FfE/SGsACYAywxPY6ZWM1X4q0bjsC2BZ4Ds+cvsqH

aAwn/SVGZPsa4BpJN+SyPtGnzwMvB26xndWKo5AirdteluvoxSikv0TfbJ8taQeq1Z1r9bRPL5cq

Guoe4Hcp0EYvRVq3XStpiu1ZpYNEK6S/RN8knQzsTlWk/RjYF7gGSJEWg90B/EzSpSw9lSJbcIwg

RVq37QbcLOlOqjfGwDyArLKJoaS/xGi8Afhr4De2j5C0PvCNwpmime6sv9asv6JPKdK6bZ/SAaJV

0l9iNP5oe7GkhZLWAR4CNi4dKprH9imlM7RVirRuy/n/GI30lxiNGyT9FXAWcCMwH/hl2UjRRJKu

YojPF9t7FojTKinSuu1HVG+MMVQTezcHTHV9xojB0l+ib7aPqW9+RdJlwDq5HmMM4597bq8FHAQs

LJSlVXLtztWIpKnAMbbfUTpLNF/6SyyLpFNtn9RzPBaYbvvQgrGiJSRdZ3ta6RxNl5G01YjtmyTt

WjpHtEP6S4xgY0kn2j5N0nOBC4HflA4VzSNpUs/hGsDOwLqF4rRKirQOk/S+nsOBN8bvC8WJhkt/

iVE6Ejhf0onAHsCltr9QOFM00408M5ViIdVKz6OKJmqJFGndNqHn9kLgh8B3C2WJ5kt/iRHVp8EH

nA58FfhP4GpJU23fVCZZNJXtzUtnaKvMSVtN1NdMG2/7sdJZovnSX2I49Uq94SzJir0YTNLBwGW2

H5f0EWAq8PEU9CNLkdZhkr5JdTHkRcD1wDrA6bY/WzRYNFL6S0SsCpJ+a3snSa8CPg58FjjJdua8

jiCnO7ttiu3HJB0KXAqcQDU3IL90YyjpLzGiQXMXnyWX+okhLKr/3Q/4mu0fSfp4yUBtsUbpALFK

PUfSc4ADgUtsLyAblsbw0l+iHxPqr12AfwI2rL+OpjqNFTHYfZK+CrwR+HG9Gjj1Rx/yP6nbvgrM

Bp4P/FzSpkDmGMVw0l9iRLZPqS/zsxEw1fb7bb+fajXwJmXTRUMdAvwE2Nv2o8Ak4ANlI7VD5qSt

ZiSNs52dnqMv6S8xHEkGdrL9VH38XOC3tlU2WTRRvdnx+vRMs7J9d7lE7ZA5aR1Wf2geBGzG0j/r

U4sEikZLf4lRmg5cJ+l79fGBwLnl4kRTSXoPcDLwILC4bl4C7FQsVEukSOu2i4F5VJO/nyqcJZov

/SX6ZvsTki4F/qZuOsJ2rjgQQzkOkO1HSgdpmxRp3baR7X1Kh4jWSH+JvtSnrm61vS2Qva5iJPdQ

/QEYo5SFA912raQdS4eI1kh/ib7YXgRYUhYKRD/uAH4m6URJ7xv4Kh2qDTKS1m2vAt4u6U6q01dj

qHYEzzyAGEr6S4zGROBWSdcBTww02t6/XKRoqLvrrzXrr+hTirRu27d0gGiV9JcYjY+WDhDtUG/Z

gqTx9fH8sonaI1twdJykv+aZib2/sP1fJfNEs6W/xMoi6Ze2X146R5QnaQdgBtX+aAB/AN5m+9Zy

qdohc9I6TNJxwPnAevXXN+ql0BHPkv4SK9lapQNEY3wNeJ/tTW1vCrwfOKtwplbI6c5uOwrY1fYT

AJI+DfwS+FLRVNFU6S+xMuU0TQx4vu2rBg5s/0zS80sGaosUad02hmcubEt9e0yhLNF86S8RsSrc

IemjVKc8Ad5KteIzRpAirdvOAX49aEfwswvmiWZLf4mVKQV+DDgSOAW4iGqE9Rd1W4wgCwc6TtJU

qq0VoJoInh3BY1jpL9GPejPby23vsYzH7GD7d3/BWBGdkyKtwyTtRrUr+OP18TrAdrZ/XTZZNFH6

S4yGpCuA19vOTvKxTJJ+Chxs+9H6eCJwge29yyZrvpzu7LYzgak9x/OHaIsYkP4SozEfuKX+Bdy7

me2x5SJFQ71woEADsD1X0nolA7VFtuDotjG2nx4qtb2YFOYxvPSXGI2LqDa0/TlwY89XxGCLey8h

JmlTsvq3L/kA7rY7JB1LNRoCcAxZURPDS3+Jvtk+T9LzgE1su3SeaLQPA9dIuppqQcnfAP9YNlI7

ZCSt244GXgHcB9wL7EreGDG89Jfom6TXATcDl9XHL5F0SdlU0US2L6OaNvEt4AJgZ9s/Gbhf0val

sjVdFg6sxiSdaPu00jmiHdJfopekG4E9gZ/Zfmnd9jvbO5RNFm0j6Sbbmfs6hIykrd4OLh0gWiX9

JXotGGJl5+IiSaLtsqfeMDInbfWWN0aMRvpL9LpV0luAsZK2Bo4Fri2cKdopp/SGkZG01VveGDEa

6S/R6z3A9sBTwH8AjwHHF00U0TEZSVu9ZWQkRiP9JZ5m+0mqVXsfLp0lWu/PpQM0VYq01du3SweI

Vkl/iadJ2gX4ELAZPb9LbO9UKlM0V32Vga2BtQbabP+8/ne3UrmaLqs7O0zSWsBRVKcket8YubBt

PEv6S4yGJAMfAG6hZ8GA7buKhYpGkvQO4DhgI6ptW3YDfml7z6LBWiAjad02A/hvYG/gVOBQ4Lai

iaLJ0l9iNB62nX3Roh/HAS8DfmV7D0nbAp8snKkVsnCg27ay/VHgCdvnAftRbVAaMZT0lxiNkyX9

u6Q3S3r9wFfpUNFIf7L9JwBJz7X934AKZ2qFjKR124L630cl7QA8AOSitjGc9JcYjSOAbYHn8Mzp

ziVU1/SM6HWvpL8Cvg/8VNJcIKfF+5Airdu+Vk/W/AhwCTAeOKlspGiw9JcYjZfZzmhIjMj2P9Q3

PybpKmBd4NKCkVojRVq3zbD9FPBzYAsASZPKRooGS3+J0bhW0hTbs0oHiWaTdJTtswFsX123fQo4

oWiwFsictG67SNJzBg4kbQD8tGCeaLb0lxiN3YCbJVnSbyXdIum3pUNFIx0k6dCBA0n/BkwumKc1

MpLWbd8HLpT0BmBjqlNY/1w2UjRY+kuMxj6lA0RrHARcImkxVb951PZRhTO1QvZJ6zhJ76Z6U2wG

vMt2rq0Xw0p/iX5J2mSodtt3/6WzRDMNmi4xgeoPwf+knutqe06JXG2SkbQOkvS+nsMxwCbUGwhK

2s3258skiyZKf4nl9COq1ZxjqDY/3hww1WbIEQA38kwfGfh3v/prCfXc1xheirRumjDo+KJh2iMg

/SWWg+0de48lTQWOKRQnmumDti+UtIXtO0qHaaMUad20le3DJB1n+/TSYaLx0l9ihdm+SVI2P45e

JwAXAt8BphbO0kqZk9ZBkm4F9qLah2Z3qiHmp2UeQPRKf4nlMeg0+RrAzsAk23sXihQNI+mnVKc1

Xwb8YvD9tvf/i4dqmYykddNXgSuozvffyNK/dDMPIAZLf4nl0Xs6fCHwQ+C7hbJEM+1HNYI2A/hc

4SytlJG0DpN0pu1/Kp0j2iH9JZaXpDWA8bYfK50lmkfSZNsPl87RRinSVgOS1qNafQVkiXwsW/pL

9EPSN4GjgUXA9cA6wOm2P1s0WDSOpMnAB4EpLP3ZsmexUC2RKw50mKTXSboduBO4GphNrpcWw0h/

iVGaUo+cHUjVTzYHDisbKRrqfOA2qj5yCtVny/UlA7VFirRu+zjVpVv+x/bmwKuBX5WNFA2W/hKj

8Zz6MmIHApfYXkA1hzFisBfU1+5cYPtq20cCGUXrQ4q0bltg+xFgDUlr2L4K2KV0qGis9JcYja9S

jYg8H/i5pE2BzEmLoSyo/71f0n6SXgpMWtYTopLVnd32qKTxwM+B8yU9BDxROFM0V/pL9M32GcAZ

PU13SdqjVJ5otI9LWhd4P/AlqvmL7y0bqR1SpHXbAcAfqd4MhwLrUs0HiBhK+kv0TdJzqS6cvRlL

/y45tUigaLJrbc8D5gF7AEjavGykdkiR1m1/a/tSYDFwHoCko4GvFE0VTZX+EqNxMdUv3RuBpwpn

iWb7gaR9B7ZokbQd8G1gh7Kxmi9FWrd9VNJTtq8EkPQBqsma+aUbQ0l/idHYyPY+pUNEK3ySqlDb

DxAwnWq0PkaQIq3b9gd+WP+y3QfYluqUVsRQ0l9iNK6VtKPtW0oHiWaz/aN6JfBMqitV/IPt/ykc

qxWymW3H1RuTXk51SuJI2/mBx7DSX6JfkmYBW1Htq/cU1eXEltjeqWiwaAxJX2LpbVleDfwv1apg

bB9bIFarZCStgyQ9ztJvjDWprr/4BklLbK9TJlk0UU9/GVP/m/4S/di3dIBovBsGHd9YJEWLZSRt

NSZpe9u3ls4REe0k6a+Bv6kPf2H7v0rmiXaS9F3bB5XO0UTZzHb1NqN0gGgOSd+V9Pf1xbIjlknS

cVSX+1mv/vqGpPeUTRUttUXpAE2VD+PV25jSAaJRzqRacXW7pE9JUulA0WhHAbvaPsn2SVSXFHtn

4UzRTjmlN4zMSVu95Y0RT7N9OXB5vTP4m+vb9wBnAd+or80YMWAMsKjneBH5wy9ipUqRFhFPk/QC

4DDgrcBvqE5nvQo4HNi9XLJooHOAX0v6Xn18IHB2wTzRXinuh5EibfX259IBojnqX7aimqv4WtsP

1Hd9S9LgVVqxmrP9eUk/oyriAY6w/ZuCkaK9Plg6QFNldWeHSbrC9qtHaosAkLQvsD3wSqpLQ10D

nGn7T0WDRSNJ2g241fbj9fE6wHa2f102WTSNpFcCHwM2pRocGthTLwsGRpCRtA6StBawNvBCSRN5

Zih5HWDDYsGi6Y4AHgPOqI/fQjWqdnCxRNFkZwJTe47nD9EWAdVp8PdS7ZO2aITHRo8Uad30LuB4

4MVUb4qBIu0x4F9LhYrG28H2lJ7jq+pd5SOGMqb3ihS2F0vK75QYyjzbl5YO0UZ5Q3WQ7dOB0yW9

x/aXSueJ1rhJ0m62fwUgaVeevWN4xIA7JB1LNXoGcAxwR8E80VxXSfoscBHVJcQAsH1TuUjtkDlp

HSfpFcBm9BTktqcXCxSNJek2qoUDd9dNmwAGFpJrMsYg9XVezwD2pNrO5wrgeNsPFQ0WjSPpqiGa

l9je8y8epmVSpHWYpBnAlsDNPDMPYEkuahtDkbTpsu63fddfKku0n6QTbZ9WOkdEm+V0Z7ftAkzp

nTcSMZwUYbGSHQykSAvqDbJPBv62broaONX2vHKp2iGXheq23wEblA4REaulbFAaA74OPA4cUn89

RrUZcowgI2nd9kJglqTrWHqy5v7lIkXEaiIj+DFgS9sH9RyfIunmYmlaJEVat32sdICIWG1lJC0G

/FHSq2xfA09vbvvHwplaIUVah9m+up4MvrXtyyWtDYwtnSsiVgvfLh0gGuNoYHo9N20MMAd4e9FE

LZHVnR0m6Z3APwKTbG8paWvgK7ksVESsqPrKJkdRXUpsrYF220cWCxWNVl86DNuPlc7SFhlJ67Z3

A9OAXwPYvr3e2ygiYkXNAP4b2Bs4FTgUuK1oomgkSc8FDqLes1MSALZPLRirFbK6s9uesv3ngYP6

ki0ZOo2IlWEr2x8FnrB9HrAfsGvhTNFMFwMHUG2M/UTPV4wgI2nddrWkDwHPk7QX1WVbflA4U0R0

w4L630cl7QA8AGSkPoayke19Sodoo4ykddsJwMPALVQXXf8x8JGiiSKiK74maSLVZ8olwCzgM2Uj

RUNdK2nH0iHaKAsHIiJi1CQ91/ZTg9om2Z5TKlM0i6RbqKbYjAO2Bu6g2rNzDLkecF9yurPDJN3J

EHPQbG9RIE5EdMtFkg60vQBA0gbAj4Cdy8aKBnlt6QBtlyKt23bpub0W1bX0JhXKEhHd8n3gQklv

ADamOuX5z2UjRZMMXA9Y0gzbh/XeJ2kGcNiQT4ynpUjrMNuPDGr6oqQbgZNK5ImI7rB9lqQ1qYq1

zYB32b62bKpoqO17DySNJSOufUmR1mGSpvYcrkE1spafeUQsN0nv6zkcA2wC3AzsJmk3258vkyya

RtKJwMAOAwMb2I4B/gx8rViwFskv7G77XM/thcBs4JAyUSKiIyYMOr5omPZYzdk+DThN0mm2Tyyd

p41SpHWY7T1KZ4iIztnK9mGSjrN9eukw0QqS9PfAZbYXlw7TJtmCo4MGnY54lpyOiIjlJelWYC/g

UmB3qtNXT8sWHDGYpNcARwC7Ad8GzrHtsqnaISNp3ZTTDhGxqnwVuALYAriRpYu0JXV7xNNsXw5c

Lmld4M317XuAs4BvDGzjEs+WkbQOkvRp2x+UdLDtb5fOExHdI+lM2/9UOke0g6QXUG258Vbg98D5

wKuAHW3vXjBao6VI66B6l+edgBttTx3p8RERy0vSelT7MAJg++6CcaKBJH0PEDCD6lTnAz333WB7

l2GfvJpLkdZBkj4LvBMYDzzZc9fApTjWKRIsIjpD0uuAzwMvBh4CNgVus739Mp8Yqx1J+1LtlfZK

YDFwDXCm7T8VDdYCmZPWQbY/AHxA0sW2DyidJyI66eNUE8Evt/1SSXtQncqKGOwI4DHgjPr4LVSj

agcXS9QSKdI6LAVaRKxCC2w/ImkNSWvYvkrSF0uHikbawfaUnuOrJM0qlqZFUqR1mKTXA58G1qM6

1ZnTnRGxsjwqaTzwc+B8SQ8BTxTOFM10U301il8BSNoVuKFwplbInLQOk/T/gNfZvq10lojoFknP

B/5Idcm5Q4F1qbZTyD5psRRJt1EtHBhYVLIJYKor4SyxvVOpbE2XkbRuezAFWkSsIn9r+1KqieDn

AUg6GvhK0VTRRPuUDtBWGUnrMEmnAxsA3weeGmi3fdGwT4qI6IOka4GP2L6yPv4AsKftfcsmi+iO

jKR12zpUW3D8XU/bEp65IHJExPLaH/hhXZztA2wLZLFSxEqUkbSIiFgu9Ua2l1NdHupI2/mFErES

pUjrMEkbAV+i2kAQ4BfAcbbvLZcqItpM0uNUI/Jj6n/XpJ4ATlaPR6xUOd3ZbecA3+SZDQPfWrft

VSxRRLSa7QmlM0SsLlKkddtk2+f0HJ8r6fhiaSKiMyR9FzgbuMz24tJ5IrooRVq3PSLprcB/1Mdv

Bh4pmCciuuNMqsv9fEnSt6kunO3CmSI6JXPSOkzSplRz0l5ONV/kWuA9tu8pGiwiOkPSulR/AH4Y

uAc4i2pT2wVFg0V0wBqlA8QqdSpwuO3JttcDjgROKZwpIjpC0guoRtPeAfwGOB2YCvy0ZK6Irsjp

zm7byfbcgQPbcyS9tGSgiOgGSd+jutTPDOC1th+o7/qWpFyXMWIlyEhat60haeLAgaRJpDCPiJXj

a8DXgWnAv0l6r6S1AGzvUjRZREfkF3a3fQ74ZT2pF6qtOD5RME9EdMcRwGPAGfXxW6hG1Q4e9hkR

MSpZONBxkqYAe9aHV9qeVTJPRHSDpFm2p4zUFhHLLyNpHVcXZSnMImJlu0nSbrZ/BSBpVyBz0SJW

ooykRUTEqEm6jWrhwN110yaAqS8RZXunUtkiuiIjaRERsTz2KR0gousykhYRERHRQNmCIyIiIqKB

UqRFRERENFCKtIiIiIgGSpEWERER0UD/H+LOqLI/YLhhAAAAAElFTkSuQmCC

)

# Limitations¶

This is not a complete overview of these clients. There are many trade-offs to
consider before you jump and pick one. First, is the confluent and rdkafka
version of pykafka are C backed and will not work with pypy. Also, packaging
and distributing python packages with C extension can be a pain. We are big
fans of conda to help alleviate this pain. We have package both
[pykafka](http://activisiongamescience.github.io/2016/06/15/Kafka-Client-
Benchmarking/\(https://anaconda.org/ActivisionGameScience/pykafka),
[confluent-kafka-client](https://anaconda.org/ActivisionGameScience/confluent-
kafka) and [librdkafka](https://anaconda.org/ActivisionGameScience/librdkafka)
on our [public conda channel](https://anaconda.org/ActivisionGameScience).

Another limitation of this benchmark approach that negatively affects the
performance of pykafka and confluent_kafka is the lack of other work other
then blindly ripping through messages. Pykafka uses a background thread to
consume messages before you ever call `consume()`. The vagaries of python
threading and limitations of the GIL are beyond the scope of this post but
keep in mind that pykafka's background will continue to poll brokers while you
do work in your program. This effect is more pronounced for any work that is
I/O bound.

The confluent_kafka client was released on May 25th, so while the underlying
librdkafka is hardened as widely used, the python client is very fresh. Also,
some of the metadata APIs are not exposed to the client. Specifically, we have
found issues with the Offset API and Offset Commit/Fetch API, so getting the
starting and ending offset for a partition and the current lag of a consumer
group is not straight forward. Confluent seems committed to the client, so I'm
sure a sane client metadata api will be added.

# Conclusion¶

Not to devolve this post into the ravings of a petulant language zealot, but
why should the JVM get all the goodies first! It is really exciting to see the
python ecosystem around Kafka develop. Having first class Python support for
Kafka is huge for our work here at Activision and for anyone who is interested
in stream processing and streaming analytics.

```python

    !docker-compose down # Cleanup
    
```

```python

    Stopping pythonkafkabenchmark_kafka_1 ... 
    Stopping pythonkafkabenchmark_zookeeper_1 ... 
    Removing pythonkafkabenchmark_kafka_1 ... 
    Removing pythonkafkabenchmark_zookeeper_1 ... 
    Removing network pythonkafkabenchmark_default
    
```

