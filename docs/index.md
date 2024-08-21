## **O que é o RabbitMQ?**

O RabbitMQ é um software de mensageria open-source amplamente utilizado para enviar e receber mensagens entre sistemas distribuídos. Ele implementa o protocolo AMQP (Advanced Message Queuing Protocol) e é conhecido por sua confiabilidade, escalabilidade e flexibilidade.

## **Principais características**

* **Filas (Queues)**: O RabbitMQ armazena as mensagens em filas, que podem ser consumidas por um ou mais consumidores. As filas garantem que as mensagens sejam entregues na ordem em que foram enviadas.

* **Produtores (Producers)**: Os produtores são responsáveis por enviar mensagens para as filas. Eles publicam mensagens para uma troca (Exchange), que, por sua vez, roteia as mensagens para as filas apropriadas.

* **Consumidores (Consumers)**: Os consumidores são responsáveis por receber as mensagens das filas e processá-las. Um consumidor pode ser qualquer aplicação ou serviço que esteja conectado ao RabbitMQ.

* **Trocas (Exchanges)**: As trocas recebem mensagens dos produtores e determinam para qual fila ou filas essas mensagens devem ser roteadas. Existem diferentes tipos de trocas, como direct, fanout, topic e headers, cada uma com regras específicas de roteamento.

* **Bindings**: Um binding é uma conexão entre uma troca e uma fila que define como as mensagens devem ser roteadas. As regras de binding determinam se uma mensagem deve ser entregue a uma fila específica.

* **Mensagens**: As mensagens em RabbitMQ podem conter qualquer tipo de dado, como texto, JSON, XML, etc. Elas são enviadas pelo produtor e processadas pelo consumidor.

## **Usos comuns**

* **Desacoplamento de serviços interconectados** 

    Você tem um serviço backend que precisa enviar notificações para os usuários finais. Existem dois canais de notificação: e-mails e notificações push para o aplicativo móvel.

    O backend publica a notificação em duas filas, uma para cada canal. Programas que gerenciam e-mails e notificações push se inscrevem na fila de seu interesse e processam as notificações assim que elas chegam.

* **Chamada de Procedimento Remoto (RPC)**

    Você é proprietário de uma casa de shows. Os ingressos para os espetáculos são vendidos em vários sites e quiosques físicos. Os pedidos de todos os canais devem passar por um processo complexo para determinar se um cliente realmente obterá seus ingressos, dependendo da disponibilidade. O site ou quiosque espera receber uma resposta para o pedido no menor tempo possível.

    Os pedidos são publicados em uma fila no RabbitMQ com um ID de correlação. O chamador que enviou o pedido, então, se inscreve em outra fila e aguarda uma resposta com o mesmo ID de correlação.

    Para alcançar baixa latência, uma fila clássica é adequada aqui, mas isso vem com o custo de menor segurança — o chamador ainda pode tentar novamente. Se o pedido não puder ser perdido, você pode preferir usar uma combinação de confirmações e filas de quórum para garantir que uma mensagem esteja segura uma vez confirmada.

    Essa topologia permite que o processamento de pedidos seja serializado para atendê-los em ordem de chegada. Isso evita a necessidade de transações.

* **Streaming**

    Você administra uma plataforma de vídeos. Quando um usuário faz o upload de um novo vídeo, há várias tarefas a serem concluídas após o vídeo ser armazenado com segurança: realizar uma análise pós-upload, transcodificar cópias de qualidade inferior do vídeo, notificar outros usuários inscritos nas criações do autor, e assim por diante.

    O serviço de upload adiciona eventos de "Novo vídeo" a um stream do RabbitMQ. Várias aplicações backend podem se inscrever nesse stream e ler novos eventos independentemente umas das outras. Os usuários devem ser notificados imediatamente, mas a análise pós-upload pode esperar e ser executada uma vez por dia.


* **IoT**

    Você oferece serviços de entrega de pacotes em toda a galáxia. Você possui um enxame de drones espaciais que precisam relatar seu status regularmente para um servidor hospedado no exoplaneta Kepler-438 b. Infelizmente, a conectividade da rede não é muito boa...

    Cada drone espacial executa um nó RabbitMQ local independente que armazena temporariamente seus relatórios até que uma conexão com o RabbitMQ upstream seja possível.

    Quando os planetas estão alinhados, o RabbitMQ do drone envia todos os relatórios para o RabbitMQ upstream.



