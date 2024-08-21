## **Primeiros passos**

Vamos cubrir os básicos do **RabbitMQ** neste tutorial.

Para começarmos precisamos instalar o RabbitMQ server, por favor siga o [guia de instalação](installing.md).

Com o RabbitMQ server instalado, iremos seguir com o tutorial em [Python](https://python.org.br/instalacao-windows/), lembrando que o RabbitMQ pode ser usado com uma gama de diferentes tecnologias.

E para isso teremos que instalar o pacote Pika 1.0.0 do python com o seguinte comando:

```cmd
python -m pip install pika --upgrade
```

Gostaria de deixar claro também que neste tutorial iremos apenas tratar das filas tradicionais.


## **Hello World!**

Nesta parte do tutorial, vamos escrever dois pequenos programas em Python: um produtor (remetente) que envia uma única mensagem e um consumidor (receptor) que recebe mensagens e as imprime. É o "Hello World" da mensageria.

#### **Enviando**

Nosso primeiro programa será o `send.py` o qual irá mandar uma mensagem para nossa fila, para isso a primeira coisa que precisamos fazer
é abrir uma conexão com o RAbbitMQ server:

```Python
#!/usr/bin/env python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()
```

Com isso estamos conectados, porém temos que verificar que a fila já existe logo:

```Python
channel.queue_declare(queue='hello')
```

Agora sim estamos prontos para mandar uma mensagem, que terá uma string *Hello World*.

```Python
channel.basic_publish(exchange='',
                      routing_key='hello',
                      body='Hello World!')
print(" [x] Sent 'Hello World!'")
```

Agora que já mandamos a mensagem, não podemos nos esquecer de fechar a conexão

```Python
connection.close()
```

#### **Recebendo**

Agora vamos consumir essa mensagem, melhor dizendo receber esta mensagem através do arquivo `receive.py`, o qual iremos seguir passos parecidos primeiro abrindo a conexão com o server e verificando se a fila existe:

```Python
#!/usr/bin/env python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')
```

Para receber mensagens pode ser um pouco mais complicado, a final precisamos de uma função de callback para que ativemos nosso consumidor assim que uma mensagem chegar:

```Python
def callback(ch, method, properties, body):
    print(f" [x] Received {body}")
```

Agora precisamos avisar o RabbitMQ que essa função callback está escutando a nossa fila:

```Python
channel.basic_consume(queue='hello',
                      auto_ack=True,
                      on_message_callback=callback)
```

E por último criamos um loop para que nosso consumidor fique esperando uma mensagem até que a aplicação seja interrompida:

```Python
print(' [*] Waiting for messages. To exit press CTRL+C')
channel.start_consuming()

if __name__ == '__main__':
    try:
        main()
    except KeyboardInterrupt:
        print('Interrupted')
        try:
            sys.exit(0)
        except SystemExit:
            os._exit(0)
```

#### **Juntando tudo**

Primeiro vamos verificar os dois arquivos construídos, o `send.py` deve estar assim:

```Python
#!/usr/bin/env python
import pika

connection = pika.BlockingConnection(
    pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

channel.basic_publish(exchange='', routing_key='hello', body='Hello World!')
print(" [x] Sent 'Hello World!'")
connection.close()
```

E o `receive.py`:

```Pyhton
#!/usr/bin/env python
import pika, sys, os

def main():
    connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
    channel = connection.channel()

    channel.queue_declare(queue='hello')

    def callback(ch, method, properties, body):
        print(f" [x] Received {body}")

    channel.basic_consume(queue='hello', on_message_callback=callback, auto_ack=True)

    print(' [*] Waiting for messages. To exit press CTRL+C')
    channel.start_consuming()

if __name__ == '__main__':
    try:
        main()
    except KeyboardInterrupt:
        print('Interrupted')
        try:
            sys.exit(0)
        except SystemExit:
            os._exit(0)
```

Agora que está tudo certo vamos testar nosso programa em um terminal.

Primeiro vamos rodar nosso consumidor:

```Python
python receive.py
# => [*] Waiting for messages. To exit press CTRL+C
```

Em seguida vamos rodar nosso produtor que irá enviar a mensagem:

```Python
python send.py
# => [x] Sent 'Hello World!'
```

Se tudo saiu como o esperado, o output deve ser assim:

```Python
# => [*] Waiting for messages. To exit press CTRL+C
# => [x] Received 'Hello World!'
```

Bom este foi o tutorial inicial, espero que posssa ter aprendido um pouco sobre esta ferramente e sobre mensagerias.

Gostaria de deixar claro que a ferramente possui muito mais do que isso, e você pode acessar a documentação completa na [documentação oficial](https://www.rabbitmq.com/)

