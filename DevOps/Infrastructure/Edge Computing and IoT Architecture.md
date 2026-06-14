---
id: a1b2c3d4-1191-4000-8000-000000000191
title: Edge Computing and IoT Architecture
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001191
---
# Edge Computing and IoT Architecture

Edge computing processes data near its source rather than in a centralized cloud. IoT (Internet of Things) devices generate data at the edge.

## Architecture Layers

```
Cloud Layer
  ┌────────────────────────────────────────────┐
  │  Data Lake, ML Training, Dashboard, API    │
  └────────────────────────────────────────────┘
           ▲                          │
           │ (aggregated data)        │ (commands)
           │                          ▼
Edge Layer
  ┌────────────────────────────────────────────┐
  │  Edge Gateway, Local Processing, Cache     │
  │  Inference at Edge, Data Filtering         │
  └────────────────────────────────────────────┘
           ▲                          │
           │ (sensor data)            │ (actuate)
           │                          ▼
Device Layer (IoT)
  ┌────────────────────────────────────────────┐
  │  Sensors, Actuators, Cameras, PLCs        │
  │  Microcontrollers (ESP32, Arduino)         │
  └────────────────────────────────────────────┘
```

## Edge vs Cloud vs Fog

| Aspect | Cloud | Fog | Edge |
|--------|-------|-----|------|
| Latency | 100-500ms | 10-100ms | 1-10ms |
| Bandwidth | High | Medium | Low |
| Processing | Massive | Moderate | Limited |
| Storage | Unlimited | Limited | Minimal |
| Power | Unlimited | Moderate | Battery |
| Examples | AWS, GCP, Azure | Edge gateway | Raspberry Pi, ESP32 |

## IoT Communication Protocols

| Protocol | Range | Bandwidth | Power | Use Case |
|----------|-------|-----------|-------|----------|
| MQTT | Internet | Low | Low | Sensor data, pub/sub |
| CoAP | LAN | Low | Low | Constrained devices |
| HTTP/2 | Internet | High | High | Rich devices |
| BLE | 10m | Medium | Very low | Wearables, beacons |
| LoRaWAN | 10km | Very low | Very low | Smart city, agriculture |
| Zigbee | 100m | Low | Low | Home automation |
| Z-Wave | 30m | Low | Low | Smart home |
| NB-IoT | 15km | Low | Low | Cellular IoT |

## MQTT Protocol

```
Publish/Subscribe pattern:

Publisher ──► MQTT Broker ──► Subscriber A
                 │
                 └──► Subscriber B

Topics: sensor/temperature, sensor/humidity
QoS levels:
  0: At most once (fire and forget)
  1: At least once (acknowledged)
  2: Exactly once (4-way handshake)
```

```python
# MQTT publisher (IoT device)
import paho.mqtt.client as mqtt

client = mqtt.Client()
client.connect("broker.example.com", 1883)

while True:
    temperature = read_sensor()
    client.publish("sensor/temperature", temperature, qos=1)
    time.sleep(60)

# MQTT subscriber (edge/cloud)
def on_message(client, userdata, msg):
    print(f"{msg.topic}: {msg.payload}")

client = mqtt.Client()
client.on_message = on_message
client.connect("broker.example.com", 1883)
client.subscribe("sensor/#", qos=1)
client.loop_forever()
```

## Edge ML Inference

```python
# TensorFlow Lite on edge device
import tflite_runtime.interpreter as tflite
import numpy as np

# Load quantized model
interpreter = tflite.Interpreter(model_path="model_quantized.tflite")
interpreter.allocate_tensors()

input_details = interpreter.get_input_details()
output_details = interpreter.get_output_details()

def predict(sensor_data):
    input_data = np.array(sensor_data, dtype=np.float32).reshape(input_details[0]["shape"])
    interpreter.set_tensor(input_details[0]["index"], input_data)
    interpreter.invoke()
    output = interpreter.get_tensor(output_details[0]["index"])
    return output

# Edge inference: 10ms vs 500ms for cloud round-trip
```

## Data Flow Pattern

```python
class EdgeProcessor:
    def __init__(self):
        self.buffer = []
        self.buffer_size = 100
        self.model = load_model()

    def process_sensor_reading(self, reading):
        # 1. Filter noise
        if not self.validate(reading):
            return

        # 2. Run edge inference
        prediction = self.model.predict(reading.features)

        # 3. Act locally if urgent
        if prediction.is_anomaly and prediction.confidence > 0.95:
            self.actuator.shutdown()
            self.send_alert(reading)

        # 4. Buffer and batch for cloud
        self.buffer.append(reading)
        if len(self.buffer) >= self.buffer_size:
            self.send_to_cloud(self.buffer)
            self.buffer.clear()
        elif self.is_slow_period():
            self.send_to_cloud(self.buffer)
            self.buffer.clear()

    def validate(self, reading):
        # Reject out-of-range values
        return -50 < reading.temperature < 150
```

## Edge Gateway Architecture

```yaml
edge_gateway:
  hardware: NVIDIA Jetson Orin / Raspberry Pi 5
  
  services:
    - name: mqtt-broker
      image: eclipse-mosquitto
      ports: [1883, 9001]
      volumes: [mosquitto_data:/data]

    - name: edge-inference
      image: edge-inference:latest
      devices: [GPU]
      volumes: [models:/models]
      environment:
        MODEL_PATH: /models/anomaly.tflite
        
    - name: data-buffer
      image: redis:alpine
      volumes: [buffer_data:/data]
      command: redis-server --save 60 1000

    - name: cloud-sync
      image: cloud-sync:latest
      environment:
        CLOUD_ENDPOINT: https://api.example.com/ingest
        BATCH_SIZE: 1000
        SYNC_INTERVAL: 300  # seconds
```

## Common Challenges

| Challenge | Solution |
|-----------|----------|
| Limited power | Sleep modes, duty cycling, energy harvesting |
| Connectivity loss | Store-and-forward, local processing |
| Device heterogeneity | Protocol translation gateways |
| Security | Hardware root of trust, encrypted comms |
| Firmware updates | OTA with signed updates, rollback |
| Scale | Hierarchical aggregation, edge caching |
