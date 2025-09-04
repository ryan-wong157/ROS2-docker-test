# ROS2 Upskilling Repo

Welcome to the ROS2 upskilling workshop! This README will guide you through five progressive tasks designed to teach you fundamental ROS2 concepts, from basic publisher/subscriber patterns to advanced launch systems and CAN communication. Although SR8 will probably not implement ROS2 at the time of writing, it is still important for existing/incoming members to understand how to write basic ROS2 code for continued development of systems on SR7.The expectation of this workshop is that upon completion, you should be able modify/develop code that is ready to be used in SR7. Deployment of said code to SR7 can be found on this [link.](https://unsw.sharepoint.com/sites/SunswiftVIP-ChallENG/SiteAssets/Sunswift%20VIP%20-%20ChallENG%20Notebook/_Collaboration%20Space/08%20DEPT%20-%20Embedded%20Systems%20Notes.one#2023-06-26%20–%20SR7%20Car%20Commands&section-id={CA86A959-5DAF-41DC-8575-B97256B9A577}&page-id={99909989-6ADA-4461-8ED0-3999E9DBFE7C}&end)


## Prerequisites

Before starting, please ensure you have:
- ROS2 installed and sourced
- Basic understanding of Python or C++
- Familiarity with terminal/command line

## Workspace Setup

If your workspace hasn't been built, build it with the following commands:
```bash
cd /root/ros2_upskilling_sr/ros2_ws
colcon build
source install/setup.bash
```
The test repo should have an automated script that builds this upon a new terminal instance being made in vscode, so this step shouldn't be necessary for you. 

---

## Task 1: Basic Publisher/Subscriber Communication (●◌◌◌◌)

### Concept
The publisher/subscriber pattern is the foundation of ROS2 communication. Publishers send data to topics, while subscribers listen to those topics. This decoupled architecture allows nodes to communicate without knowing about each other directly.

### What You'll Build
Create two nodes:
- A **publisher node** that sends a counter value every 50ms
- A **subscriber node** that receives and displays the counter

### Basic Commands
```bash
# Create a new package
cd src
ros2 pkg create --build-type ament_cmake --license Apache-2.0 <package_name>

# After implementing your nodes, build and run:
colcon build

# Run publisher (in terminal 1)
ros2 run your_package_name_here node_name_here

# Run subscriber (in terminal 2)  
ros2 run your_package_name_here node_name_here

# Check topics
ros2 topic list
```

### Key Concepts to Implement
- Create a publisher using `self.create_publisher()`
- Create a timer with `self.create_timer()` for 50ms (0.05 seconds)
- Create a subscriber using `self.create_subscription()`
- Use `std_msgs/Int64` message type

### Additional Resources
Refer to the ROS2 Publisher/Subscriber tutorial [here](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Publisher-And-Subscriber.html)

---

## Task 2: Custom Messages with Timestamps (●●◌◌◌)

### Concept
Custom messages allow you to define your own data structures for communication. This is essential when standard message types don't meet your needs. Either make new packages for this task, or reuse the ones already existing in task one

### What You'll Build
Create a custom message containing:
- `time` (UTC timestamp)
- `number` (counter value)  
- `message` (formatted string: "message {counter} sent at time {time}")

Send this message every 100ms between nodes.

### Basic Commands
```bash
# Create a message package
ros2 pkg create --build-type ament_cmake my_custom_msgs
# create two new directories in the new package - per ros2 tutorials website:
mkdir msg srv


# Build messages first
colcon build --packages-select msg_name_file_here
source install/setup.bash

# Then build your publisher/subscriber package
colcon build

# Test your custom message by then running your new nodes. 
```

### Key Concepts to Implement
- Define custom message in `.msg` file
- Add message dependencies in `CMakeLists.txt` and `package.xml`
- Use `self.get_clock().now().to_msg()` for timestamps
- Convert ROS2 time to UTC for display
- Format strings with counter and time values

### Need More Help?
Refer to the ROS2 Custom Messages Tutorial [here](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Custom-ROS2-Interfaces.html#)

---

## Task 3: Service Communication (●●●◌◌)

### Concept
Services provide synchronous request/response communication, unlike the asynchronous publish/subscribe pattern. Services are ideal for actions that need a response or confirmation. Use your existing implementations from previous tasks, or remake the functionality outlined in task 1 for this task. 

### What You'll Build
Create a service that:
- Accepts a counter value as a request
- Returns "request received" if the counter equals 12, 55, 67, 102, or 105
- Returns a different message for other values

### Basic Commands
```bash
# Create service definition in package (or create new srv package)
# Add .srv file defining request/response structure

# Create service server and client nodes

# Run service server
ros2 run my_service_pkg counter_service_server_name_here

# Test with service client
ros2 run my_service_pkg counter_service_client_here

# Or test manually
ros2 service call /counter_check my_custom_msgs/srv/CounterCheck "{counter: 55}"
ros2 service list
```

### Key Concepts to Implement
- Define custom service in `.srv` file
- Create service server with `self.create_service()`
- Create service client with `self.create_client()`
- Handle synchronous request/response pattern
- Implement conditional logic for specific counter values

### Need More Help?
Refer to the ROS2 Services Tutorial [here.](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Custom-ROS2-Interfaces.html#)

---

## Task 4: Launch Files and System Integration (●●●●◌)

### Concept
Launch files allow you to start multiple nodes simultaneously with predefined configurations. This is essential for complex systems with many interconnected nodes.

### What You'll Build
Create a launch system that:
- Uses a **global launch file** in the `global_launch` package
- Includes **individual launch files** from each package
- Starts all your nodes (publisher, subscriber, service) together

### Basic Commands
```bash
# Create launch files in launch/ directories
# global_launch/launch/global_launch.py should include other launch files

colcon build
source install/setup.bash

# Launch entire system
ros2 launch global_launch global_launch.py

# Check running nodes
ros2 node list

# Monitor system
ros2 topic list
ros2 service list
```

### Key Concepts to Implement
- Create Python launch files using `LaunchDescription`
- Use `IncludeLaunchDescription` to include other launch files
- Set parameters and remappings in launch files
- Organize launch files hierarchically
- Use `Node()` declarations for individual nodes

### Need More Help?
Refer to the ROS2 Launch System Tutorial [here.](https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Launch/Launch-Main.html)

---

## Task 5: CAN Bus Communication (●●●●●)

### Concept
Controller Area Network (CAN) is a robust communication protocol commonly used in automotive and industrial applications. ROS2 can interface with CAN networks to integrate with real hardware systems.
### It is important to note that more likely than not, you will not be able to test this locally. Once finished, please message Henry or Ryan to see if your code works. 

### What You'll Build
Create a CAN counter node that:
- Reads incoming CAN packets from the SR8 test bed
- Processes CAN frame data  
- Publishes counter information to ROS2 topics
- Uses the existing `ros2_socketcan` package



### Key Concepts to Implement
- Understand CAN frame structure (ID, data, flags)
- Use `ros2_socketcan` package for CAN communication
- Subscribe to `/from_can_bus` topic
- Parse CAN frame data to extract counter values
- Handle different CAN IDs and data formats
- Integrate with your existing ROS2 nodes

### Need More Help?
Refer to the ROS2 CAN Communication Tutorial [here](https://docs.ros.org/en/jazzy/p/ros2_socketcan/)

If you are still unsure, you can view Ryan's reference implementation [here](https://github.com/ryan-wong157/ROS2-docker-test)

---

## Testing Your Progress

you can use these commands to verify your implementation after each task:

```bash
# Check all nodes are running
ros2 node list

# Monitor topics
ros2 topic list
ros2 topic hz /your_topic_name  # Check frequency

# Test services
ros2 service list
ros2 service type /your_service_name

# View computation graph
ros2 run rqt_graph rqt_graph
```

## Debugging Tips

- Use `ros2 doctor` to check system health
- Use `ros2 topic echo` to debug message flow
- Check logs with `ros2 log` commands
- Use `colcon build --symlink-install` for faster Python development


