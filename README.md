# helyos_agent_slim_simulator

It simulates an agent in the helyOS framework. It can be used for front-end development or for testing path planning algorithms.

## Getting started

Run with the default settings:
```
docker run helyosframework/agent_helyos_slim_simulator
```

Or use the docker-compose shown in `/run/docker-compose.yml` or build your own.

For development purposes you could run slim simulator in native python by installing the requirements.txt file. 
Therefore it's necessaary to check the enivornment varibales in `/run/.env`. Tested in python 3.8 with helyos_sdk=0.8.0.
Both deployment options - running in docker or natively - are reading the environment from `/run/.env`!

## Building a docker image

```
docker build --no-cache -t helyosframework/helyos_agent_slim_simulator:test .
```
or
```
docker buildx build --platform linux/amd64 -t helyosframework/helyos_agent_slim_simulator:x86  --no-cache . --load
```

## Assignment data formats
### Trajectory

```python
assignment = { "operation": "driving", "trajectory": [{"x": float, "y": float, "orientations":List[float], time:float}, ...] }

```
### Destination point
``` python
assignment = { "operation": "driving", "destination": {"x": float, "y": float, "orientations":List[float]}  }
```

### AutoTruck-TruckTrix path format

https://app.swaggerhub.com/apis-docs/helyOS/Tructrix_API/4.0#/TrucktrixTrajectory


## Instant actions
Besides the helyOS-required instant actions (mission reserve, mission release and cancel),
we have implemented additional instant actions triggered by the following strings:

* "pause" or "resume" : pause/resume a running assignment.
* "tail lift up" or "tail lift down": change the value of the tail lift sensor.
* "headlight on" or "headlight off": change the value of the tail lift sensor.

## Customizations

The `/src/customizations` folder contains scripts and configurations that allow for further customization of the simulator:

| File                          | Description                                        |
| ----------------------------- | -------------------------------------------------- |
| `agent_sensors.py`            | Defines the sensor data for the simulated agent.   |
| `connected_tool_sensors.py`   | Manages sensor data for tools connected to the agent. |
| `custom_instant_actions.py`   | Implements additional instant actions for the simulator. |
| `shared_data.pys`             | Contains geometric configurations for the simulation. |
| `geometry.json`               | Customize the sensors initial data and access communication channels. |


## Configuration and available settings in `/run/.env`

The simulator is configured by the environment variables:

| VARIABLE | DESCRIPTION |
| --- | --- |
| UUID | String with unique identifcation code of agent (use "RANDOM_UUID" for auto-generated uuids) |
| REGISTRATION_TOKEN | Allow agent to check in even if not registered in helyOS |
| NAME | Agent name |
| YARD_UID | Yard identifier |
| UPDATE_RATE | Frequency of published messages (Hz) |
| --- | --- |
| PATH_TRACKER |  ideal (arb. unit), stanley (mm), straight_to_destination(arb.unit)|
| ASSIGNMENT_FORMAT | fixed, trajectory, destination, trucktrix-path |
| VEHICLE_PARTS | Number of parts. eg. truck with trailer: 2 |
| --- | --- |
| X0 | Initial horizontal position (arb. unit, dep. PATH_TRACKER)|
| Y0 | Initial vertical position (arb. unit, dep. PATH_TRACKER)|
| ORIENTATION | Initial orientation in mrads |
| VELOCITY | Driving velocity 0 to 10. (arb. unit) |
| --- | --- |
| RABBITMQ_HOST | HelyOS RabbitMQ Server  |
| RBMQ_VHOST | Virtual host of RabbitMQ   |
| RABBITMQ_PORT | HelyOS RabbitMQ Port (e.g.,5671, 5672, 1883, 8883, default:5672)  |
| RBMQ_USERNAME | Agent RabbitMQ account name |
| RBMQ_PASSWORD | Agent RabbitMQ account password  |
| PROTOCOL | "AMQP" or "MQTT" (default: AMPQ)  |
| ENABLE_SSL | True or False (default: False)  |


For `ENABLE_SSL`=True, you must copy the server host CA certificate to the location app/ca_certificate.pem. Check in `./example`.

Optional environmnet variable for the `stanley` path tracker:

| VARIABLE | DESCRIPTION |
| --- | --- |
| STANLEY_K | control gain (default: 1) |
| STANLEY_KP | speed proportional gain (default: 0.5)|
| STANLEY_L |  Wheel base of vehicle length (m) (default: 2.9) |
| STANLEY_MAXSTEER | (rad) max steering angle (default: 12)|
| --- | --- |

Ref:
    - [Stanley: The robot that won the DARPA grand challenge](http://isl.ecst.csuchico.edu/DOCS/darpa2005/DARPA%202005%20Stanley.pdf)
    - [Autonomous Automobile Path Tracking](https://www.ri.cmu.edu/pub_files/2009/2/Automatic_Steering_Methods_for_Autonomous_Automobile_Path_Tracking.pdf)



### License

This project is licensed under the MIT License.