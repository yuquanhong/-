![1558146500613](/home/yuquanhong/.config/Typora/typora-user-images/1558146500613.png)

添加用户

![1558156523816](/home/yuquanhong/.config/Typora/typora-user-images/1558156523816.png)

创建virtual host虚拟主机

![1558156582137](/home/yuquanhong/.config/Typora/typora-user-images/1558156582137.png)

授权

![1558156803902](/home/yuquanhong/.config/Typora/typora-user-images/1558156803902.png)



![1558158648123](/home/yuquanhong/.config/Typora/typora-user-images/1558158648123.png)

生产者:

```java
package com.thr.rubbitmq.simple;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.thr.rubbitmq.Connectionutils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Send {
    public static final String QUEUE_NAME = "queue_test";
    public static void main(String[] args) throws IOException, TimeoutException {
        //获取连接
        Connection connection = Connectionutils.getConnect();
        //从连接获取一个通道
        Channel channel = connection.createChannel();
        //创建队列声明
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        String msg = "hello simple!";
        //发布消息
        channel.basicPublish("", QUEUE_NAME, null, msg.getBytes());
        channel.close();
        connection.close();
    }
}
```

消费者:

```java
package com.thr.rubbitmq.simple;


import com.rabbitmq.client.*;
import com.thr.rubbitmq.Connectionutils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 消费消息
 */
public class Recv {
    public static final String QUEUE_NAME = "queue_test";

    public static void main(String[] args) throws IOException, TimeoutException {
        Connection
        connection = Connectionutils.getConnect();

        Channel channel = connection.createChannel();

        channel.basicConsume(QUEUE_NAME,new DefaultConsumer(channel){
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.printf("recv:"+ new String(body));
            }
        });

    }
}
```

![1558161926521](/home/yuquanhong/.config/Typora/typora-user-images/1558161926521.png)

![1558162077601](/home/yuquanhong/.config/Typora/typora-user-images/1558162077601.png)

生产者:

```java
package com.thr.rubbitmq.work;


import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.thr.rubbitmq.Connectionutils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Send {
    public static final String QUEUE_NAME = "test_work_queue";
    public static void main(String[] args) throws IOException, TimeoutException, InterruptedException {
        Connection connection = Connectionutils.getConnect();
        Channel channel = connection.createChannel();
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        for (int i = 0; i < 50; i++) {
            String msg = "[work] hello work" + i;
            channel.basicPublish("", QUEUE_NAME, null, msg.getBytes());
            Thread.sleep(30);

        }
        channel.close();
        connection.close();
    }
}
```

消费者１：

```java
package com.thr.rubbitmq.work;

import com.rabbitmq.client.*;
import com.thr.rubbitmq.Connectionutils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Recv {
    public static final String QUEUE_NAME = "test_work_queue";

    public static void main(String[] args) throws IOException, TimeoutException {

        Connection connection = Connectionutils.getConnect();
        Channel channel = connection.createChannel();

        //定义一个消费者
        Consumer consumer = new DefaultConsumer(channel){

            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                super.handleDelivery(consumerTag, envelope, properties, body);
                String msg = new String(body, "utf-8");
                System.out.println("[1] Recv msg:" + msg);
                try{
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        boolean autoAck = true;
        channel.basicConsume(QUEUE_NAME, autoAck, consumer);
    }
}
```

消费者２：

```java
package com.thr.rubbitmq.work;

import com.rabbitmq.client.*;
import com.thr.rubbitmq.Connectionutils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Recv2 {
    public static final String QUEUE_NAME = "test_work_queue";

    public static void main(String[] args) throws IOException, TimeoutException {

        Connection connection = Connectionutils.getConnect();
        Channel channel = connection.createChannel();

        //定义一个消费者
        Consumer consumer = new DefaultConsumer(channel){

            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                super.handleDelivery(consumerTag, envelope, properties, body);
                String msg = new String(body, "utf-8");
                System.out.println("[2] Recv msg:" + msg);
                try{
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        boolean autoAck = true;
        channel.basicConsume(QUEUE_NAME, autoAck, consumer);
    }
}
```

![1558163809942](/home/yuquanhong/.config/Typora/typora-user-images/1558163809942.png)

公平分发 fair  dispatch

​    **channel.basicQos(1);**

**channel.basicAck(envelope.getDeliveryTag(), false);**

**boolean autoAck = true;**

生产者

```java
com.thr.rubbitmq.workfair;


import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.thr.rubbitmq.Connectionutils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Send {
    public static final String QUEUE_NAME = "test_work_queue";
    public static void main(String[] args) throws IOException, TimeoutException, InterruptedException {
        Connection connection = Connectionutils.getConnect();
        Channel channel = connection.createChannel();
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        /**
         * 每个消费者　发送确认消息之前，消息队列　不发送下一个消息到
         * 消费者。－次处理一个，限制发送给同一个消费者
         */
        channel.basicQos(1);

        for (int i = 0; i < 50; i++) {
            String msg = "[work] hello work" + i;
            channel.basicPublish("", QUEUE_NAME, null, msg.getBytes());
            Thread.sleep(30);

        }
        channel.close();
        connection.close();
    }
}
```

消费者一

```
package com.thr.rubbitmq.workfair;

import com.rabbitmq.client.*;
import com.thr.rubbitmq.Connectionutils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Recv2 {
    public static final String QUEUE_NAME = "test_work_queue";

    public static void main(String[] args) throws IOException, TimeoutException {

        Connection connection = Connectionutils.getConnect();
        final Channel channel = connection.createChannel();
        channel.basicQos(1);
        //定义一个消费者
        Consumer consumer = new DefaultConsumer(channel){

            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                super.handleDelivery(consumerTag, envelope, properties, body);
                String msg = new String(body, "utf-8");
                System.out.println("[2] Recv msg:" + msg);
                try{
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    channel.basicAck(envelope.getDeliveryTag(), false);
                }
            }
        };
        //取消自动应答
        boolean autoAck = false;
        channel.basicConsume(QUEUE_NAME, autoAck, consumer);
    }
}
```

消费者二

```
package com.thr.rubbitmq.workfair;

import com.rabbitmq.client.*;
import com.thr.rubbitmq.Connectionutils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Recv {
    public static final String QUEUE_NAME = "test_work_queue";

    public static void main(String[] args) throws IOException, TimeoutException {

        Connection connection = Connectionutils.getConnect();
        final Channel channel = connection.createChannel();
        channel.basicQos(1);
        //定义一个消费者
        Consumer consumer = new DefaultConsumer(channel){

            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                super.handleDelivery(consumerTag, envelope, properties, body);
                String msg = new String(body, "utf-8");
                System.out.println("[1] Recv msg:" + msg);
                try{
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    channel.basicAck(envelope.getDeliveryTag(), false);
                }
            }
        };
        //取消自动应答
        boolean autoAck = true;
        channel.basicConsume(QUEUE_NAME, autoAck, consumer);
    }
}
```

现象消费者一比消费者二接收的多，能者多劳。



![1558166001093](/home/yuquanhong/.config/Typora/typora-user-images/1558166001093.png)

持久化：

```java
/**
 * Declare a queue
 * @see com.rabbitmq.client.AMQP.Queue.Declare
 * @see com.rabbitmq.client.AMQP.Queue.DeclareOk
 * @param queue the name of the queue
 * @param durable true if we are declaring a durable queue (the queue will survive a server restart)
 * @param exclusive true if we are declaring an exclusive queue (restricted to this connection)
 * @param autoDelete true if we are declaring an autodelete queue (server will delete it when no longer in use)
 * @param arguments other properties (construction arguments) for the queue
 * @return a declaration-confirm method to indicate the queue was successfully declared
 * @throws java.io.IOException if an error is encountered
 */
Queue.DeclareOk queueDeclare(String queue, boolean durable, boolean exclusive, boolean autoDelete,
                             Map<String, Object> arguments) throws IOException;
```

durable = true来控制持久化。已经声明的队列无法修改。

订阅模式

![1558166686738](/home/yuquanhong/.config/Typora/typora-user-images/1558166686738.png)

```java
package com.thr.rubbitmq.ps;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.thr.rubbitmq.Connectionutils;

public class Send {

    public static final String EXCHANGE_NAME="text_exchange_fanout";
    public static void main(String[] args) throws Exception {
        Connection connection = Connectionutils.getConnect();
        Channel channel = connection.createChannel();
        //声明交换机
        channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
        String msg = "hello ps";
        channel.basicPublish(EXCHANGE_NAME, "", null, msg.getBytes());
        System.out.printf("send:" + msg);
        channel.close();
        connection.close();
    }
}
```

![1558167447843](/home/yuquanhong/.config/Typora/typora-user-images/1558167447843.png)

消息并没有存储，因为交换机没有存储的能力，在rabbitmq里面只有队列有存储能力，因为这时候还没有队列绑定到这个交换机上。所以数据丢失了。

消费者（一和二两个消费者一样）

```
package com.thr.rubbitmq.ps;

import com.rabbitmq.client.*;
import com.thr.rubbitmq.Connectionutils;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Recv1 {
    public static final String EXCHANGE_NAME = "text_exchange_fanout";

    public static final String QUEUE_NAME = "test_exchange_fanout_queue";

    public static void main(String[] args) throws IOException, TimeoutException {

        Connection connection = Connectionutils.getConnect();
        final Channel channel = connection.createChannel();
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "");
        channel.basicQos(1);
        //定义一个消费者
        Consumer consumer = new DefaultConsumer(channel) {

            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                super.handleDelivery(consumerTag, envelope, properties, body);
                String msg = new String(body, "utf-8");
                System.out.println("[1] Recv msg:" + msg);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    channel.basicAck(envelope.getDeliveryTag(), false);
                }
            }
        };
        //取消自动应答
        boolean autoAck = false;
        channel.basicConsume(QUEUE_NAME, autoAck, consumer);
    }
}
```

![1558168561342](/home/yuquanhong/.config/Typora/typora-user-images/1558168561342.png)

６。Exchange(交换器，转发器)

一方面是接收生产者的消息，另一方面是向队列推送消息

匿名转发的exchange设为""

fanout不处理路由键

![1558168777289](/home/yuquanhong/.config/Typora/typora-user-images/1558168777289.png)

Direct 处理路由键

![1558168865181](/home/yuquanhong/.config/Typora/typora-user-images/1558168865181.png)

路由模式:

![1558168969904](/home/yuquanhong/.config/Typora/typora-user-images/1558168969904.png)

topic

模型图:

![1558170787037](/home/yuquanhong/.config/Typora/typora-user-images/1558170787037.png)

![1558171483537](/home/yuquanhong/.config/Typora/typora-user-images/1558171483537.png)

```
public class Send {
    public static final String QUEUE_NAME = "queue_test";
    public static void main(String[] args) throws IOException, TimeoutException {
        //获取连接
        Connection connection = Connectionutils.getConnect();
        //从连接获取一个通道
        Channel channel = connection.createChannel();
        //创建队列声明
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        String msg = "hello simple!";
        try {
            channel.txSelect();
            //发布消息
            channel.basicPublish("", QUEUE_NAME, null, msg.getBytes());
            channel.txCommit();
        } catch (Exception e) {
            channel.txSelect();
        }

        channel.close();
        connection.close();
    }
}
```



![1558172314935](/home/yuquanhong/.config/Typora/typora-user-images/1558172314935.png)

发单条的confirm

```java
channel.confirmSelect();
channel.basicPublish("", QUEUE_NAME, null, msg.getBytes());
if (!channel.waitForConfirms()) {
    System.out.printf("发送失败");
}else{
    System.out.printf("发送成功");
}
```

批量发多条

```java
channel.confirmSelect();
for (int i = 0; i < 100; i++) {
    channel.basicPublish("", QUEUE_NAME, null, msg.getBytes());
}
if (!channel.waitForConfirms()) {
    System.out.printf("发送失败");
}else{
    System.out.printf("发送成功");
}
```

![1558173846122](/home/yuquanhong/.config/Typora/typora-user-images/1558173846122.png)

```java
final SortedSet<Long> confirmSet = (SortedSet<Long>) Collections.synchronizedSet(new TreeSet<Long>());
channel.confirmSelect();
channel.addConfirmListener(new ConfirmListener() {
    //成功的
    public void handleAck(long deliveryTag, boolean multiple) throws IOException {
        if(multiple){
            confirmSet.headSet(deliveryTag + 1).clear();
        }else{
            confirmSet.remove(deliveryTag);
        }
    }

    public void handleNack(long deliveryTag, boolean multiple) throws IOException {
        if (multiple) {
            confirmSet.headSet(deliveryTag +1).clear();
        }else {
            confirmSet.remove(deliveryTag);
        }
    }
});
while (true) {
    long segNo = channel.getNextPublishSeqNo();
    channel.basicPublish("", QUEUE_NAME, null, msg.getBytes());
    confirmSet.add(segNo);
}
```

