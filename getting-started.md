# Getting Started
### Choosing a borker
Celery supports various message broker.  
If you wnat more information, check [here](https://github.com/celery/kombu#transport-comparison).  

Now celery.node supports Redis and AMQP, which are recommended when using celery.

#### Redis
[Redis](https://redis.io/)  
If you want to run it on Docker, execute below.
```bash
$ docker run -d -p 6379:6379 redis
```

#### AMQP - RabbitMQ
[RabbitMQ](https://www.rabbitmq.com/)  
If you want to run it on Docker, execute below.
```bash
$ docker run -d -p 5672:5672 rabbitmq
```

## Installing celery.node
> Note that you must have node and npm installed.

```bash
$ npm install celery-node
```
Now you're ready to use `celery-node`.
Let's take a look what is a simple example.

## Usage
### Worker
#### celery.node
```javascript
const celery = require('celery-node');

/*
  Create celery worker instance with amqp broker and amqp result backend.
*/
const worker = celery.createWorker(
  "amqp://",
  "amqp://"
);

/*
  Register celery task defining what worker do based on task name. 
*/
worker.register("tasks.add", (a, b) => a + b);

/*
  Worker start.
*/
worker.start();
```
#### celery (python)
Equivalant python codes below.
```python
from celery import Celery

app = Celery('tasks',
    broker='amqp://',
    backend='amqp://'
)
  
@app.task
def add(x, y):
    return x + y
```
```bash
$ celery worker -A tasks -l info
```
### Client
#### celery.node
```javascript
const celery = require('celery-node');

/*
  Create celery client instance with amqp broker and amqp result backend.
*/
const client = celery.createClient(
  "amqp://",
  "amqp://"
);

/*
  Create celery task with task name. 
  When task invokes applyAsync, worker will take a task and do function based on task name.
*/
const task = client.createTask("tasks.add");
const result = task.applyAsync([1, 2]);

/*
  Get result from the result backcend which is defined when create client.
*/
result.get().then(data => {
  console.log(data);
  client.disconnect();
});
```
#### celery (python)
Equivalant python codes below.
```python
from celery import Celery

app = Celery('tasks',
    broker='amqp://',
    backend='amqp://'
)

@app.task
def add(x, y):
    return x + y

if __name__ == '__main__':
    result = add.apply_async((1, 2), serializer='json')
    print(result.get())
```