# Kafka

### Producer
~~~Py
from confluent_kafka import Producer

def delivery_callback(err, msg):
    if err:
        print(f'Message failed delivery: {err}\n')
    else:
        print(f'Message delivered to {msg.topic()} [{msg.partition()}]\n')

def createTopic():
    print("init")
    
    topic = '{Tópico_karafka}'

    conf = {
        'bootstrap.servers': '{servidor_karafka},{exemplo:tricycle-01.srvs.cloudkafka.com:9094}', #separados por vírgula, mesma str
        #'session.timeout.ms': 6000,
        #'default.topic.config': {'auto.offset.reset': 'smallest'}, essas confs são exclusivas do consumer
        'security.protocol': 'SASL_SSL',
	    'sasl.mechanisms': 'SCRAM-SHA-256',
        'sasl.username': '{user_cluster_karafka_ex:25fi2ymi}',
        'sasl.password': '{senha_cluster_karafka}'
    }

    p = Producer(conf)

    try:
        p.produce(topic, "mensagem para o broker", callback=delivery_callback)
    except BufferError as e:
        print(f'Local producer queue is full ({len(p)} messages awaiting delivery): try again\n')

    p.poll(0)

    print(f'Waiting for {len(p)} deliveries\n')
    p.flush()

createTopic()

# Conf do consumer -> https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md
~~~

### Consumer
~~~Py
import sys
from confluent_kafka import Consumer, KafkaException, KafkaError

def createConsumer():

    topics = ['{tópico_karafka}']

    conf = {
        'bootstrap.servers': '{servidor_karafka}',
        'group.id': "%s-consumer" % '{sasl_username}',
        'session.timeout.ms': 6000,
        'default.topic.config': {'auto.offset.reset': 'smallest'},
        'security.protocol': 'SASL_SSL',
	    'sasl.mechanisms': 'SCRAM-SHA-256',
        'sasl.username': '{user_cluster_karafka}',
        'sasl.password': '{senha_cluster_karafka}'
    }

    c = Consumer(conf)
    c.subscribe(topics)
    try:
        while True:
            msg = c.poll(timeout=1.0)
            if msg is None:
                continue
            if msg.error():
                # Error or event
                if msg.error().code() == KafkaError._PARTITION_EOF:
                    # End of partition event
                    sys.stderr.write(f'{msg.topic()} [{msg.partition()}] reached end at offset {msg.offset()}\n')
                elif msg.error():
                    # Error
                    raise KafkaException(msg.error())
            else:
                # Proper message
                sys.stderr.write(f'{msg.topic()} [{msg.partition()}] at offset {msg.offset()} with key {msg.key()}:\n')
                print(msg.value().decode('UTF-8')) #... caso não queira uma b-string

    except KeyboardInterrupt:
        sys.stderr.write('Aborted by user\n')

    # Close down consumer to commit final offsets.
    c.close()

createConsumer()

# Conf consumer -> https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md
~~~