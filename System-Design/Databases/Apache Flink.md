---
id: a1b2c3d4-1172-4000-8000-000000000172
title: Apache Flink
language: markdown
tags: [system-design, databases, flink, stream-processing]
selection: null
isPinned: false
timestamp: 1781500001172
---

**Links**: [[Stream Processing]] | [[Apache Kafka Deep Dive]] | [[Apache Spark]] | [[Event Sourcing Deep Dive]] | [[Data Engineering]] | [[Message Queues]]


# Apache Flink

Apache Flink is a distributed stream processing framework with true event-time processing, exactly-once semantics, and stateful computations.

## Architecture

```
JobManager (master)
    │
    ├── ResourceManager
    ├── Dispatcher
    └── JobMaster (per job)
         │
    TaskManagers (workers)
         │
    ┌────┴────┐
    │  Slot 0 │ Operator Subtask
    │  Slot 1 │ Operator Subtask
    │  Slot 2 │ Operator Subtask
    └─────────┘
```

## Core Abstractions

| API | Level | Use Case |
|-----|-------|----------|
| ProcessFunction | Lowest | Custom state, timers, complex logic |
| DataStream | Low | Stream transformations |
| Table/SQL | High | Declarative queries |
| CEP (Complex Event Processing) | High | Pattern matching on streams |

## DataStream API

```java
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

DataStream<String> lines = env.socketTextStream("localhost", 9999);

DataStream<WordCount> counts = lines
    .flatMap((String line, Collector<String> out) -> {
        for (String word : line.split(" ")) {
            out.collect(word.toLowerCase());
        }
    })
    .returns(Types.STRING)
    .map(word -> new WordCount(word, 1))
    .keyBy(wc -> wc.word)
    .window(TumblingProcessingTimeWindows.of(Time.seconds(5)))
    .sum("count");

counts.print();

env.execute("WordCount");
```

## Event Time and Watermarks

```java
// Event time processing
env.setStreamTimeCharacteristic(TimeCharacteristic.EventTime);

DataStream<Event> stream = env
    .addSource(kafkaConsumer)
    .assignTimestampsAndWatermarks(
        WatermarkStrategy
            .<Event>forBoundedOutOfOrderness(Duration.ofSeconds(5))
            .withTimestampAssigner((event, timestamp) -> event.getTimestamp())
    );

// Watermark: "I've seen all events up to time T"
// Late events (within allowed lateness) are still processed
// Very late events go to side output
```

## State Management

```java
// Keyed state (per key)
DataStream<SensorReading> readings = env.addSource(sensorSource);

readings
    .keyBy(r -> r.sensorId)
    .process(new KeyedProcessFunction<String, SensorReading, Alert>() {

        private ValueState<Double> lastTemp;
        private ValueState<Long> timerState;

        @Override
        public void open(Configuration parameters) {
            lastTemp = getRuntimeContext().getState(
                new ValueStateDescriptor<>("lastTemp", Types.DOUBLE));
            timerState = getRuntimeContext().getState(
                new ValueStateDescriptor<>("timer", Types.LONG));
        }

        @Override
        public void processElement(SensorReading reading, Context ctx, Collector<Alert> out) {
            Double prev = lastTemp.value();
            if (prev != null && Math.abs(reading.temperature - prev) > 10) {
                out.collect(new Alert(reading.sensorId, "Temperature spike"));
            }
            lastTemp.update(reading.temperature);
        }
    });
```

## State Backends

| Backend | Storage | Checkpoint | Use Case |
|---------|---------|------------|----------|
| MemoryStateBackend | JVM heap | JobManager memory | Small state, dev |
| FsStateBackend | JVM heap | File system | Medium state |
| RocksDBStateBackend | RocksDB (disk) | File system | Large state (GBs) |

```java
env.setStateBackend(new RocksDBStateBackend("hdfs://namenode/flink/checkpoints"));
env.getCheckpointConfig().setCheckpointingMode(CheckpointingMode.EXACTLY_ONCE);
env.getCheckpointConfig().setCheckpointInterval(60_000);
env.getCheckpointConfig().setMinPauseBetweenCheckpoints(30_000);
env.getCheckpointConfig().setTolerableCheckpointFailureNumber(3);
```

## Window Types

```java
// Tumbling (non-overlapping)
stream.keyBy(e -> e.id).window(TumblingEventTimeWindows.of(Time.hours(1)));

// Sliding (overlapping)
stream.keyBy(e -> e.id).window(SlidingEventTimeWindows.of(Time.hours(1), Time.minutes(15)));

// Session (gap-based)
stream.keyBy(e -> e.id).window(EventTimeSessionWindows.withGap(Time.minutes(5)));

// Global (custom trigger)
stream.keyBy(e -> e.id).window(GlobalWindows.create()).trigger(CustomTrigger.of());
```

## Checkpointing and Savepoints

```java
// Checkpoints (automatic, for recovery)
env.enableCheckpointing(60_000);
env.getCheckpointConfig().setCheckpointingMode(CheckpointingMode.EXACTLY_ONCE);

// Savepoints (manual, for upgrades)
// $ flink savepoint <jobId> <targetDir>
// $ flink run -s <savepointPath> -d job.jar
```

## Flink SQL

```sql
-- Register tables
CREATE TABLE orders (
    order_id BIGINT,
    customer_id BIGINT,
    amount DECIMAL(10,2),
    order_time TIMESTAMP(3),
    WATERMARK FOR order_time AS order_time - INTERVAL '5' SECOND
) WITH (
    'connector' = 'kafka',
    'topic' = 'orders',
    'properties.bootstrap.servers' = 'localhost:9092',
    'format' = 'json'
);

-- Streaming query
SELECT
    TUMBLE_END(order_time, INTERVAL '1' HOUR) AS window_end,
    customer_id,
    SUM(amount) AS total_spent,
    COUNT(*) AS order_count
FROM orders
GROUP BY
    TUMBLE(order_time, INTERVAL '1' HOUR),
    customer_id;
```

## Exactly-Once Semantics

| Component | Exactly-Once Mechanism |
|-----------|----------------------|
| Kafka source | Kafka offset committed with checkpoint |
| Flink state | Checkpoint + aligned barriers |
| Kafka sink | Two-phase commit (KafkaTransactionLogger) |

## Performance

| Metric | Typical |
|--------|---------|
| Throughput | Millions events/sec per node |
| Latency | Milliseconds (streaming) |
| State size | Up to TB (RocksDB) |
| Parallelism | Thousands of operators |
